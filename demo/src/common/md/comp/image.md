%%Overview

选择与预处理图片，产出可用于 `FormData` 上传的图片对象

%%Basic

基础的 `b-image` 无需依赖配置项

点击图片会 emit `change` 事件，并将图片对象更新至 `value`

%%BasicCode

```html
<template>
    <b-image v-model="image"></b-image>
</template>

<script>
    export default {
        data() {
            return {
                image: null
            };
        }
    };
</script>
```

%%Multiple

`multiple` 为 `true` 时可选择多张图片；此时 value 为图片对象的数组

%%MultipleCode

```html
<template>
    <b-image v-model="images" multiple></b-image>
</template>

<script>
    export default {
        data() {
            return {
                images: []
            };
        }
    };
</script>
```

%%PreviewMode

当用户选择图片后，此时有两种模式来展示这些图片的预览：

* **内部自动处理**：由 `b-image` 内部管理图片预览的展示，如上面两个例子所示；此时开发者无需处理预览相关内容
* **外部手动处理**：外部传入图片 uri list，此时开发者可以在选择后直接上传图片至远端服务器取得这些图片信息来渲染图片预览，这样可以保持预览图片上传成功才显示或经过服务端处理

props `preview-mode` 用来控制图片预览由内部或外部控制，可选择 `INSIDE`，`OUTSIDE`，默认 `INSIDE`

`preview-mode` 为 `OUTSIDE` 时，传入 `preview-list` 控制图片预览:

```html
<b-image
    :preview-list="images"
    preview-mode="OUTSIDE"
    multiple
/>
```

`preview-list` 是一个 `Array`，每个元素形如

```
{
    uri // 可以是 base64 编码的 String 或远端图片资源 url
}
```

通常，外部控制图片预览需要开发者监听 `add`, `remove`, `reset` 事件，从而处理 `preview-list`

```
<b-image
    :preview-list="images"
    multiple
    preview-mode="OUTSIDE"
    @add="onAdd"
    @remove="onRemove"
    @reset="onReset"
/>
...
onAdd(files) {
    // files 为用户选择的增量图片
}

onRemove(index) {
    // index 为删除的图片 index
}

onReset() {
    // handle reset
}
```

%%PreviewModeCode

```html
<template>
    <b-form-group label="Inside">
        <b-image
            v-model="inside"
            multiple
            preview-mode="INSIDE"
        />
    </b-form-group>

    <b-form-group label="Outside">
        <b-image
            :preview-list="outside"
            multiple
            preview-mode="OUTSIDE"
            @add="onOutsideAdd"
            @remove="onOutsideRemove"
            @reset="onReset"
        />
    </b-form-group>
</template>

<script>
    export default {
        data() {
            return {
                inside: [],
                outside: []
            };
        },

        methods: {
            onOutsideAdd(data) {
                const vm = this;
                uploadImages(data).then((uriList) => {
                    uriList.forEach(uri => {
                        vm.outside.push({uri});
                    });
                });
            },

            onOutsideRemove(index) {
                this.outside.splice(index, 1);
            },

            onReset() {
                this.outside = [];
            }
        }
    };
</script>
```

%%Consistent

`b-date-string` 满足以下表单组件一致性要求：
* 组合 `b-form-group`
* 默认值
* `disbaled`
* `name`
* `placeholder`
* `reset`

%%API

#### Props

|Name|Type|Desc|Default|
|:-:|:-:|:-:|:-:|
|`value`|`String`|model value|`null`|
|`name`|`String`|表单 name|`''`|
|`disabled`|`Boolean`|是否禁用|`false`|
|`placeholder`|`String`|输入栏提示|`''`|
|`enable-reset`|`Boolean`|是否可重置|`true`|
|`multiple`|`Boolean`|是否支持多选|`false`|
|`preview-mode`|`['INSIDE', 'OUTSIDE']`|内部或外部控制图片预览模式|`'INSIDE'`|
|`preview-list`|`Array`|`preview-mode` 在 `'OUTSIDE'` 模式下预览图片列表|`[]`|

#### Events

|Name|Type|Desc|
|:-:|:-:|:-:|
|`change`|`(value: [File, FileList]) => void`|图片选择时触发|
|`add`|`(value: FileList) => void`|多选下增加图片时触发|
|`remove`|`(index: Number) => void`|多选下删除图片时触发|