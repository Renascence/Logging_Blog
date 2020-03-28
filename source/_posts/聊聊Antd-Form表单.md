---
title: 聊聊Antd Form表单
date: 2020-03-28 13:10:37
tags: React Antd
---

# Antd Form

ant design的表单相信大家都已经很熟悉了，今天聊聊其中几个api都具体实现。

----

先来看一个demo：

```
class NormalLoginForm extends React.Component {
  handleSubmit = e => {this.props.form.validateFields((err, values) => {});};
  render() {
    const { getFieldDecorator } = this.props.form;
    return (
      <Form onSubmit={this.handleSubmit} className="login-form">
        <Form.Item>
          {getFieldDecorator('username', {
            rules: [{ required: true, message: 'Please input your username!' }],
          })(
            <Input
              prefix={<Icon type="user" style={{ color: 'rgba(0,0,0,.25)' }} />}
              placeholder="Username"
            />,
          )}
        </Form.Item>
        <Form.Item>
          {getFieldDecorator('password', {
            rules: [{ required: true, message: 'Please input your Password!' }],
          })(
            <Input
              prefix={<Icon type="lock" style={{ color: 'rgba(0,0,0,.25)' }} />}
              type="password"
            />,
          )}
        </Form.Item>
        <Form.Item>
          <Button type="primary" htmlType="submit" className="login-form-button">
            Log in
          </Button>
        </Form.Item>
      </Form>
    );
  }
}
const WrappedNormalLoginForm = Form.create({ name: 'normal_login' })(NormalLoginForm);

ReactDOM.render(<WrappedNormalLoginForm />, mountNode);

```


![Demo](https://raw.githubusercontent.com/Renascence/Renascence.github.io/hexo/source/_posts/img/formdemo.jpg)

其中主要的几个api是 Form.creat， getFieldDecorator, form.validateFields。

先看最外层的 Form.creat,这个方法创造了一个高阶组件，给传入的组件props上传入了一些方法。


```
import createDOMForm from 'rc-form/lib/createDOMForm';

export default class Form extends React.Component<FormProps, any> {
  …  
  static create = function create(options = {}) { 
    // create方法主要调用了createDOMForm函数 
    return createDOMForm({
      fieldNameProp: 'id',
      …options,
      fieldMetaProp: FIELD_META_PROP,
      fieldDataProp: FIELD_DATA_PROP,
    });
  };··
  renderForm = ({ getPrefixCls }: ConfigConsumerProps) => { // 给formItem增加类名，用于控制样式 
    …
    const formClassName = classNames(
      prefixCls,
      {
        [`${prefixCls}-horizontal`]: layout === 'horizontal',
        [`${prefixCls}-vertical`]: layout === 'vertical',
        [`${prefixCls}-inline`]: layout === 'inline',
        [`${prefixCls}-hide-required-mark`]: hideRequiredMark,
      },
      className,
    );
     … 
     return <form {...formProps} className={formClassName} />;
  };
  render() {
    const { wrapperCol, labelAlign, labelCol, layout, colon } = this.props;
    return (
      <FormContext.Provider
        value={{ wrapperCol, labelAlign, labelCol, vertical: layout === 'vertical', colon }}
      >
        <ConfigConsumer>{this.renderForm}</ConfigConsumer>
      </FormContext.Provider>
    );
  }
}

```

![Fields](https://raw.githubusercontent.com/Renascence/Renascence.github.io/hexo/source/_posts/img/formdemo.jpg)
初始化完成的fieldsStore，其中fields和fieldsMeta为存储信息的对象，其他方法都是对这两个对象对读写操作。

fields主要用来记录每个表单项的实时数据，包含以下属性：
 - dirty 数据是否已经改变，但未校验
 - errors 校验文案
 - name 字段名称
 - touched 数据是否更新过
 - value 字段的值
 - validating 校验状态

fieldsMeta 用来记录元数据，即每个字段对应组件的配置信息：
 - initialValue 初始值
 - name 字段的名称
 - trigger 触发数据收集的时机 默认 onChange
 - valuePropName 子节点的值的属性，例如 checkbox 应该设为 checked
 - rules 校验的规则
 - originalProps 被 getFieldDecorator() 装饰的组件的原始 props
 - validate 校验规则和触发事件

然后看rc-form库里的createDOMForm方法。

```
// createDOMForm.js
function createDOMForm(option) {
  return createBaseForm({
    ...option,
  }, [mixin]);
}
export default createDOMForm;
```

```
// createBaseForm.js
getInitialState() {
  const fields = mapPropsToFields && mapPropsToFields(this.props);
  this.fieldsStore = createFieldsStore(fields || {}); 
  // 初始化创建fieldsStore，用于存储表单信息
  … 
  return { submitting: false };
}
render() 
  const { wrappedComponentRef, ...restProps } = this.props;
  const formProps = {
    [formPropName]: this.getForm(), 
    // 来在 formPropName默认为form，getForm方法来自`createForm.js`，注入props.form的api
    {/*
      getForm() {
        return {
          getFieldsValue: this.fieldsStore.getFieldsValue,
          getFieldInstance: this.getFieldInstance,
          setFieldsValue: this.setFieldsValue,
          setFieldsInitialValue: this.fieldsStore.setFieldsInitialValue,
          getFieldDecorator: this.getFieldDecorator,
          getFieldsError: this.fieldsStore.getFieldsError,
          isSubmitting: this.isSubmitting,
          submit: this.submit,
          validateFields: this.validateFields,
          resetFields: this.resetFields,
          ...
        };
      }
    */}
  };
  if (withRef) {
    formProps.ref = 'wrappedComponent';
  } else if (wrappedComponentRef) {
    formProps.ref = wrappedComponentRef;
  }
  const props = mapProps.call(this, {
    ...formProps,
    ...restProps,
  });
  return <WrappedComponent {...props} />;
}

```

### getFieldDecorator

这个方法帮我们做了数据对双向绑定，让组件变成了受控组件。

```
// createBaseForm.tsx
getFieldDecorator(name, fieldOption) {
  const props = this.getFieldProps(name, fieldOption);
  return fieldElem => {
    this.renderFields[name] = true;

    const fieldMeta = this.fieldsStore.getFieldMeta(name);
    const originalProps = fieldElem.props;
    fieldMeta.originalProps = originalProps;
    fieldMeta.ref = fieldElem.ref;
    return React.cloneElement(fieldElem, {
      ...props,
      …this.fieldsStore.getFieldValuePropValue(fieldMeta), 
      // {value: ‘xxx’}，将value注入到props上，双向绑定
    });
  };
}
```
前面提到，form内部状态都是维护在fields和fieldsMeta里的，那么getFieldDecorator肯定对fieldsMeta有做了处理，这一步包含在了 getFieldMeta 中。

```
    getFieldProps(name, usersFieldOption = {}) {
        delete this.clearedFieldMetaCache[name];
        const fieldOption = {
          name,
          trigger: DEFAULT_TRIGGER,
          valuePropName: 'value',
          validate: [],
          ...usersFieldOption,
        };
        …
        const fieldMeta = this.fieldsStore.getFieldMeta(name);
        const inputProps = {
          ...this.fieldsStore.getFieldValuePropValue(fieldOption),
          ref: this.getCacheBind(name, `${name}__ref`, this.saveRef),
        };
        …
        const validateRules = normalizeValidateRules(
          validate,
          rules,
          validateTrigger,
        );
        const validateTriggers = getValidateTriggers(validateRules);
        validateTriggers.forEach(action => {
          if (inputProps[action]) return;
          inputProps[action] = this.getCacheBind(
            name,
            action,
            this.onCollectValidate, 
            // 当组件值变化调用 onCollectValidate，写入fields   
          );
        });
        const meta = {
          ...fieldMeta,
          ...fieldOption,
          validate: validateRules,
        };
        this.fieldsStore.setFieldMeta(name, meta);
        …
        // This field is rendered, record it
        this.renderFields[name] = true;
        return inputProps;
      },
```

```
    getFieldProps(name, usersFieldOption = {}) {
        delete this.clearedFieldMetaCache[name];
        const fieldOption = {
          name,
          trigger: DEFAULT_TRIGGER,
          valuePropName: 'value',
          validate: [],
          ...usersFieldOption,
        };
        …
        const fieldMeta = this.fieldsStore.getFieldMeta(name);
        const inputProps = {
          ...this.fieldsStore.getFieldValuePropValue(fieldOption),
          ref: this.getCacheBind(name, `${name}__ref`, this.saveRef),
        };
        …
        const validateRules = normalizeValidateRules(
          validate,
          rules,
          validateTrigger,
        );
        const validateTriggers = getValidateTriggers(validateRules);
        validateTriggers.forEach(action => {
          if (inputProps[action]) return;
          inputProps[action] = this.getCacheBind(
            name,
            action,
            this.onCollectValidate, 
            // 当组件值变化调用 onCollectValidate，写入fields   
          );
        });
        const meta = {
          ...fieldMeta,
          ...fieldOption,
          validate: validateRules,
        };
        this.fieldsStore.setFieldMeta(name, meta);
        // 这一步把信息写入了fieldMeta对象
        …
        // This field is rendered, record it
        this.renderFields[name] = true;
        return inputProps;
      },
```


### validateFields

validateFields 方法获取表单中对值并且根据校验规则对value进行校验。

```
validateFields(ns, opt, cb) {
  const pending = new Promise((resolve, reject) => {
    const { names, options } = getParams(ns, opt, cb);
    let { callback } = getParams(ns, opt, cb);
    if (!callback || typeof callback === 'function') {
      const oldCb = callback;
      callback = (errors, values) => {
        if (oldCb) {
          oldCb(errors, values);
        }
        if (errors) {
          reject({ errors, values });
        } else {
          resolve(values);
        }
      };
    } // 兼容 v4从回调改为promise 
    const fieldNames = names
      ? this.fieldsStore.getValidFieldsFullName(names)
      : this.fieldsStore.getValidFieldsName();
    const fields = fieldNames
      .filter(name => {
        const fieldMeta = this.fieldsStore.getFieldMeta(name);
        return hasRules(fieldMeta.validate);
      })
      .map(name => {
        const field = this.fieldsStore.getField(name);
        field.value = this.fieldsStore.getFieldValue(name);
        return field;
      });
    if (!fields.length) {
      callback(null, this.fieldsStore.getFieldsValue(fieldNames));
      return;
    }
    return e;
  });
  // 从fieldsMeta中取出规则进行校验，使用 async-validator 
  this.validateFieldsInternal(
      fields,
      {
        fieldNames,
        options,
      },
      callback,
    );
  });
  pending.catch(e => {
    return e;
  });
  return pending;
}
```