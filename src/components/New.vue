<template>
  <div class="new">
       <div class="saveOrPreview">
            <Button type="primary" @click="save('content')">保存</Button>
            <Button @click="preview()">预览</Button>
        </div>
        <div class="newOuter">
            <div class="controlLibrary">
                <p class="tip">可以直接拖拽哦</p>
                <Button
                    v-for="dragDropButton in dragDropButtonList"
                    :key="dragDropButton.id" 
                    :id="dragDropButton.type"
                    :draggable="true"
                    class="dragDropButton" 
                >{{dragDropButton.name}}</Button>
            </div>
            <div class="mainForm">
                <div class="content" id="content"></div>
            </div>
            <div class="controlSet" id="controlSet">
                <p class="setTitle">可以设置<span class="elementType">{{controlName}}</span>控件属性哦</p>
                <div v-for="controlAttribute in controlAttributeList" :key="controlAttribute.id" class="controlAttribute" id="controlAttribute">
                    <label>
                        <span class="ruleName">{{controlAttribute.ruleName}}</span>
                        <span v-if="controlAttribute.topTip" class="topTip">{{' (' + controlAttribute.topTip + ')'}}</span>
                    </label>
                    <div className="settingsOuter">
                        <input 
                            class="settings"
                            :type="getInputType(controlAttribute.ruleType)"
                            :placeholder="controlAttribute.placeHolder" 
                            :name="controlAttribute.ruleValue"
                            v-model="controlAttribute.defaultValue"
                        />
                        <!-- <Checkbox 
                            v-if="controlAttribute.ruleType === 'checkbox'"
                            class="settings"
                            v-model="controlAttribute.defaultValue"
                        /> -->
                        <span class="ruleValue">{{controlAttribute.checkboxLabel}}</span>
                    </div>
                    <p class="bottomTip">{{controlAttribute.bottomTip}}</p>
                </div>
            </div>
        </div>
  </div>
</template>

<script>
export default {
    name: 'New',
    data() {
        return {
            // 拖拽元素
            trueDropElementChild: {
                'input': '<div class="target input"><div class="targetLabel">单行输入框</div><div class="targetValue">请输入</div><div class="closeIcon" name="closeIcon">X</div></div>',
                'textarea': '<div class="target textarea" style="height: 80px!important; line-height: 80px!important;"><div class="targetLabel">多行输入框</div><div class="targetValue">请输入</div><div class="closeIcon" name="closeIcon">X</div></div>',
                'inputNumber': '<div class="target inputNumber" draggable="true"><div class="targetLabel">数字输入框</div><div class="targetValue">请输入</div><div class="closeIcon" name="closeIcon">X</div></div>',
                'money': '<div class="target money" draggable="true" style="margin-bottom: 30px!important;"><div class="targetLabel">金额</div><div class="targetValue">请输入</div><div class="closeIcon" name="closeIcon">X</div><div class="hintWord"> 大写: <span>壹仟元整</span></div></div>',
                'radio': '<div class="target radio" draggable="true"><div class="targetLabel">单选框</div><div class="targetValue">请选择</div><div class="closeIcon" name="closeIcon">X</div></div>',
                'checkbox': '<div class="target checkbox" draggable="true"><div class="targetLabel">多选框</div><div class="targetValue">请选择</div><div class="closeIcon" name="closeIcon">X</div></div>',
                'date': '<div class="target datetime" draggable="true"><div class="targetLabel">日期</div><div class="targetValue">请选择</div><div class="closeIcon" name="closeIcon">X</div></div>',
                'daterange': '<div class="target datetimerange startDate" draggable="true" style="margin-bottom: 1px;"><div class="targetLabel">开始日期</div><div class="targetValue">请选择</div><div class="closeIcon" name="closeIcon">X</div></div><div class="target datetimerange stopDate" draggable="true"><div class="targetLabel">结束日期</div><div class="targetValue">请选择</div><div class="closeIcon" name="closeIcon">X</div></div>',
                'picture': '<div class="target image" draggable="true"><div class="targetLabel">上传图片</div><div class="closeIcon" name="closeIcon">X</div></div>',
                'file': '<div class="target file" draggable="true"><div class="targetLabel">上传附件</div><div class="closeIcon" name="closeIcon">X</div></div>',
                'remark': '<div class="target remark" draggable="true"><div class="targetLabel"></div><div class="targetValue">请输入说明文字</div><div class="closeIcon" name="closeIcon">X</div></div>',
                'refselect': '<div class="target refselect" draggable="true"><div class="targetLabel">参照选择</div><div class="targetValue">请选择</div><div class="closeIcon" name="closeIcon">X</div></div>'
            },
            // 可拖拽按钮列表
            dragDropButtonList: [
                {
                    id: '1',
                    type: 'input',
                    name: 'Input',
                },
                {
                    id: '2',
                    type: 'textarea',
                    name: 'Textarea',
                },
                {
                    id: '3',
                    type: 'inputNumber',
                    name: 'InputNumber',
                },
                {
                    id: '4',
                    type: 'money',
                    name: 'Money',
                },
            ],
            // 控件名称--在右侧设置控件属性的最上面显示
            controlName: '单选框',
            // 控件属性
            controlAttributeList: [],

            // 中间控件和右边控件属性的链接
            type_attrs: {
                input: [
                    {
                        id: "1",
                        ruleName: "标题",
                        ruleValue: "label",
                        ruleType: "text",
                        defaultValue: "",
                        placeHolder: '请输入',
                        topTip: "最多20字",
                        bottomTip: "",
                        checkboxLabel: '',
                    },
                    {
                        id: '2',
                        ruleName: '提示文字',
                        ruleValue: 'placeHolder',
                        ruleType: 'text',
                        defaultValue: '',
                        placeHolder: '请输入',
                        topTip: '最多50字',
                        bottomTip: '内容最多可填写1000字',
                        checkboxLabel: ''
                    },
                    {
                        id: '3',
                        ruleName: '必填',
                        ruleValue: 'isRequired',
                        ruleType: 'checkbox',
                        defaultValue: '',
                        placeHolder: '',
                        topTip: '',
                        bottomTip: '',
                        checkboxLabel: '是否必填'
                    },
                    {
                        id: '4',
                        ruleName: '禁用',
                        ruleValue: 'isDisabled',
                        ruleType: 'checkbox',
                        defaultValue: '',
                        placeHolder: '',
                        topTip: '',
                        bottomTip: '',
                        checkboxLabel: '是否禁用'
                    },
                    {
                        id: '5',
                        ruleName: '打印',
                        ruleValue: 'isPrint',
                        ruleType: 'checkbox',
                        defaultValue: '',
                        placeHolder: '',
                        topTip: '如不勾选，打印时不显示该项',
                        bottomTip: '',
                        checkboxLabel: '参与打印'
                    },
                    {
                        id: '6',
                        ruleName: '最大长度',
                        ruleValue: 'maxLength',
                        ruleType: 'text',
                        defaultValue: '',
                        placeHolder: '',
                        topTip: '最多200',
                        bottomTip: '',
                        checkboxLabel: ''
                    },
                ],
                textarea: [
                    {
                        id: "1",
                        ruleName: "标题",
                        ruleValue: "label",
                        ruleType: "text",
                        defaultValue: "",
                        placeHolder: '请输入',
                        topTip: "最多20字",
                        bottomTip: "",
                        checkboxLabel: '',
                    },
                    {
                        id: '2',
                        ruleName: '提示文字',
                        ruleValue: 'placeHolder',
                        ruleType: 'text',
                        defaultValue: '',
                        placeHolder: '请输入',
                        topTip: '最多50字',
                        bottomTip: '内容最多可填写1000字',
                        checkboxLabel: ''
                    },
                    {
                        id: '3',
                        ruleName: '必填',
                        ruleValue: 'isRequired',
                        ruleType: 'checkbox',
                        defaultValue: '',
                        placeHolder: '',
                        topTip: '',
                        bottomTip: '',
                        checkboxLabel: '是否必填'
                    },
                    {
                        id: '4',
                        ruleName: '禁用',
                        ruleValue: 'isDisabled',
                        ruleType: 'checkbox',
                        defaultValue: '',
                        placeHolder: '',
                        topTip: '',
                        bottomTip: '',
                        checkboxLabel: '是否禁用'
                    },
                    {
                        id: '5',
                        ruleName: '打印',
                        ruleValue: 'isPrint',
                        ruleType: 'checkbox',
                        defaultValue: '',
                        placeHolder: '',
                        topTip: '如不勾选，打印时不显示该项',
                        bottomTip: '',
                        checkboxLabel: '参与打印'
                    },
                    {
                        id: '6',
                        ruleName: '最大长度',
                        ruleValue: 'maxLength',
                        ruleType: 'text',
                        defaultValue: '',
                        placeHolder: '',
                        topTip: '最多200',
                        bottomTip: '',
                        checkboxLabel: ''
                    },
                ],
                inputNumber: [
                    {
                        id: "1",
                        ruleName: "标题",
                        ruleValue: "label",
                        ruleType: "text",
                        defaultValue: "",
                        placeHolder: '请输入',
                        topTip: "最多20字",
                        bottomTip: "",
                        checkboxLabel: '',
                    },
                    {
                        id: '2',
                        ruleName: '提示文字',
                        ruleValue: 'placeHolder',
                        ruleType: 'text',
                        defaultValue: '',
                        placeHolder: '请输入',
                        topTip: '最多50字',
                        bottomTip: '内容最多可填写1000字',
                        checkboxLabel: ''
                    },
                    {
                        id: '3',
                        ruleName: '必填',
                        ruleValue: 'isRequired',
                        ruleType: 'checkbox',
                        defaultValue: '',
                        placeHolder: '',
                        topTip: '',
                        bottomTip: '',
                        checkboxLabel: '是否必填'
                    },
                    {
                        id: '4',
                        ruleName: '禁用',
                        ruleValue: 'isDisabled',
                        ruleType: 'checkbox',
                        defaultValue: '',
                        placeHolder: '',
                        topTip: '',
                        bottomTip: '',
                        checkboxLabel: '是否禁用'
                    },
                    {
                        id: '5',
                        ruleName: '打印',
                        ruleValue: 'isPrint',
                        ruleType: 'checkbox',
                        defaultValue: '',
                        placeHolder: '',
                        topTip: '如不勾选，打印时不显示该项',
                        bottomTip: '',
                        checkboxLabel: '参与打印'
                    },
                    {
                        id: '6',
                        ruleName: '最大长度',
                        ruleValue: 'maxLength',
                        ruleType: 'text',
                        defaultValue: '',
                        placeHolder: '',
                        topTip: '最多200',
                        bottomTip: '',
                        checkboxLabel: ''
                    },
                ],
                money: [
                    {
                        id: "1",
                        ruleName: "标题",
                        ruleValue: "label",
                        ruleType: "text",
                        defaultValue: "",
                        placeHolder: '请输入',
                        topTip: "最多20字",
                        bottomTip: "",
                        checkboxLabel: '',
                    },
                    {
                        id: '2',
                        ruleName: '提示文字',
                        ruleValue: 'placeHolder',
                        ruleType: 'text',
                        defaultValue: '',
                        placeHolder: '请输入',
                        topTip: '最多50字',
                        bottomTip: '内容最多可填写1000字',
                        checkboxLabel: ''
                    },
                    {
                        id: '3',
                        ruleName: '必填',
                        ruleValue: 'isRequired',
                        ruleType: 'checkbox',
                        defaultValue: '',
                        placeHolder: '',
                        topTip: '',
                        bottomTip: '',
                        checkboxLabel: '是否必填'
                    },
                    {
                        id: '4',
                        ruleName: '禁用',
                        ruleValue: 'isDisabled',
                        ruleType: 'checkbox',
                        defaultValue: '',
                        placeHolder: '',
                        topTip: '',
                        bottomTip: '',
                        checkboxLabel: '是否禁用'
                    },
                    {
                        id: '5',
                        ruleName: '打印',
                        ruleValue: 'isPrint',
                        ruleType: 'checkbox',
                        defaultValue: '',
                        placeHolder: '',
                        topTip: '如不勾选，打印时不显示该项',
                        bottomTip: '',
                        checkboxLabel: '参与打印'
                    },
                    {
                        id: '6',
                        ruleName: '最大长度',
                        ruleValue: 'maxLength',
                        ruleType: 'text',
                        defaultValue: '',
                        placeHolder: '',
                        topTip: '最多200',
                        bottomTip: '',
                        checkboxLabel: ''
                    },
                ],
            },

            // 发送到后端的数据
            sendAttributeList: [],
        }
    },
    methods: {
        // 获取要发送到后端的控件属性数据
        getSendAttributeList(className, attribute) {
            let attributeList = [];
            let elements = document.getElementsByClassName(className); // 拖拽过来的div
            for (let i = 0; i < elements.length; i++) {
                // 获取初始化设置的属性
                let element = JSON.parse(elements[i].getAttribute(attribute));
                element['id'] = elements[i].getAttribute('id');
                attributeList.push(element);
            }
            return attributeList;
        },

        save(type) {
            // 由于将所有的数据都保存到右侧的dom属性 current-attrs 上面了。所以不需要参数了，直接将右侧的保存即可了
            let attributeParams = JSON.parse(document.getElementById('controlSet').getAttribute('current-attribute'));
            if (!attributeParams) {
                return;
            }
            // 将修改的数据挂到右侧表单上，后面要取
            if (attributeParams) {
                attributeParams.list = this.controlAttributeList;
            }
            // 将修改后的数据保存到dom节点上
            let currentElement = document.getElementById(attributeParams['id']);
            if (type == 'content') {
                if (currentElement) {
                    currentElement.setAttribute('component-attribute', JSON.stringify(attributeParams));
                }
                this.sendAttributeList = this.getSendAttributeList('comcom', 'component-attribute');
            } else if (type == 'detail') {
                if (currentElement) {
                    currentElement.setAttribute('component-detail-attribute', JSON.stringify(attributeParams));
                }
                this.sendAttributeList = self.getComData('comcomDetail', 'componentDetailAttrs');
            }
            console.log(this.sendAttributeList);
        },
        preview() {

        },

        clearControlSet() {
            // 清除属性
            document.getElementById('controlSet').removeAttribute('current-attribute');
            // 清除组件
            this.controlAttributeList = [];
        },
        getId() {
            return Number(Math.random().toString().substr(3, 3) + Date.now()).toString(36);
        },
        // 创建拖拽元素
        createDropElement(eleType) {
            let element;
            // 根据拖拽类型选择已经定义的实际放置的元素
            let trueDropElement = this.trueDropElementChild[eleType];
            // 创建元素并赋值
            element = document.createElement('div');
            element.innerHTML = trueDropElement;
            // 创建的元素设置动态id和拖拽属性
            element.setAttribute('id', this.getId());
            element.setAttribute('draggable', true);
            return element;
        },
        // 设置控件属性：中间控件和右边控件属性的联动
        setControlAttribute(element) {
            let inputs = document.querySelectorAll('#controlSet .settings');
            let targetLabelElement = element.querySelectorAll('.targetLabel')[0];
            let targetValueElement = element.querySelectorAll('.targetValue')[0];
            for (let i = 0; i < inputs.length; i++) {
                inputs[i].onchange = function () {
                    let name = this.getAttribute('name');
                    switch (name) {
                        case 'label':
                            targetLabelElement.innerHTML = this.value;
                            break;
                        case 'placeHolder':
                            targetValueElement.innerHTML = this.value;
                            // if (element.childNodes[1]) {
                            //     element.querySelectorAll('.targetVal')[0].innerHTML = this.value;
                            // }
                            break;
                        case 'isRequired':
                            if(this.checked) {
                                targetLabelElement.classList.add('targetLabelRequired');
                            } else {
                                targetLabelElement.classList.remove('targetLabelRequired');
                            }
                            break;
                        case 'isDisabled':
                            if(this.checked) {
                                targetValueElement.classList.add('targetValueDisabled');
                            } else {
                                targetValueElement.classList.remove('targetValueDisabled');
                            }
                            break;
                        // case 'isPrint':
                        //     break;    
                        // case 'maxLength':
                        //     break;                
                        default:
                            break;
                    }
                };
            }
        },

        
        handleDragover(eleId) {
            let contentElement = document.getElementById(eleId);
            contentElement.ondragover = (event) => {
                event.preventDefault()
            }   
        },
        handleDrop(eleId) {
            let contentElement = document.getElementById(eleId);
            contentElement.ondrop = (event) => {
                event.preventDefault();
                // 获取拖拽元素，从而判断拖拽元素是从左侧拖动过来的，还是自身拖动过来的。
                let eleType = event.dataTransfer.getData('Text'); // input/textarea
                if (eleType === '' || eleType === undefined) {
                    console.error('出错了');
                    return;
                }
                // 拖拽的时候就保存数据
                if (eleId == 'content') {
                    this.save('content');
                } else if (eleId == 'detail') {
                    this.save('detail');
                }
                let element;
                if(this.trueDropElementChild[eleType]) {
                    // 清除右侧控件属性设置
                    this.clearControlSet();

                    // 新建拖拽元素
                    element = this.createDropElement(eleType);

                    // 创建的元素初始化设置属性
                    if (this.controlAttributeList.length > 0) {
                        this.type_attrs[eleType] = this.controlAttributeList;
                    }
                    // 这里将id加进去
                    let clone_type_attrs = JSON.parse(JSON.stringify(this.type_attrs)); // 深拷贝防止源数据被污染
                    let attributeParams = {
                        list: clone_type_attrs[eleType],
                        type: eleType,
                        id: element.getAttribute('id'),
                    };
                    
                    if (!JSON.stringify(attributeParams)) {
                        return;
                    }

                    // 中间content挂载控件属性对象
                    if (eleId == 'content') {
                        element.setAttribute('component-attribute', JSON.stringify(attributeParams));
                        // 设置下class，用来选择dom
                        element.setAttribute('class', 'comcom');
                    } else if (eleId == 'detail') {
                        element.setAttribute('component-detail-attribute', JSON.stringify(attributeParams));
                        element.setAttribute('class', 'comcomDetail');
                    }

                    // 右侧控件属性设置
                    document.getElementById('controlSet').setAttribute('current-attribute', JSON.stringify(attributeParams));
                    this.controlAttributeList = attributeParams['list'];
                } else {
                    // 说明是自身的拖拽, 当前拖动的元素
                    // element = document.getElementById(eleType);
                }
                // 拖拽元素放置
                var targetElementId = event.toElement.id || event.toElement.parentElement.id;
                if (targetElementId == eleId) {
                    // 如果为container,元素放置在末尾
                    document.getElementById(eleId).appendChild(element);
                } else {
                    // 如果为container里的元素，则插入该元素之前
                    // this.insertBefore(element, document.getElementById(targetElementId));
                }

                // 新添加的，拖拽过来之后设置右边和中间动态响应
                let self = this;
                setTimeout(function () {
                    self.setControlAttribute(element);
                }, 0);
            }
        },
        handleClick(eleId) {
            let contentElement = document.getElementById(eleId);
            let self = this;
            contentElement.onclick = (event) => {
                // 保存右侧数据
                if (eleId == 'content') {
                    self.save('content');
                } else if (eleId == 'detail') {
                    self.save('detail');
                }

                // 获取中间的控件属性数据
                let targetParentElement = event.toElement.parentElement;
                let attributeParams;
                if (eleId == 'content') {
                    attributeParams = targetParentElement.getAttribute('component-attribute') || targetParentElement.parentElement.getAttribute('component-attribute');
                } else if (eleId == 'detail') {
                    attributeParams = targetParentElement.getAttribute('component-detail-attribute') || targetParentElement.parentElement.getAttribute('component-detail-attribute');
                }
                if (!attributeParams) {
                    return;
                }

                // 将中间的控件属性数据设置到右边的控件属性设置中
                document.getElementById('controlSet').setAttribute('current-attribute', attributeParams);
                self.controlAttributeList = JSON.parse(attributeParams)['list'];
                // 控件属性设置
                var targetElementId = targetParentElement.getAttribute('id') || targetParentElement.parentElement.getAttribute('id');
                var targetElement = document.getElementById(targetElementId);
                setTimeout(function () {
                    self.setControlAttribute(targetElement);
                }, 0);
            }
        },
        handleContentBlur(eleId) {
            let contentElement = document.getElementById(eleId);
            let self = this;
            contentElement.onblur = () => {
                if (eleId == 'content') {
                    self.save('content');
                } else if (eleId == 'detail') {
                    self.save('detail');
                }
            }   
        },
        handleControlAttributeBlur(eleId) {
            let controlAttributeElement = document.getElementById(eleId);
            let self = this;
            controlAttributeElement.onblur = () => {
                self.saveData('content');
                self.saveData('detail');
            }
        },

        // 拖放核心
        handleDragDrop(eleId) {
            this.handleDragover(eleId);
            this.handleDrop(eleId);
            this.handleClick(eleId);
            this.handleContentBlur(eleId);
            this.handleControlAttributeBlur('controlSet');
        },

        // 开始拖拽的时候添加id(即type)
        handleDragstart() {
            let dragButtons = document.getElementsByClassName('dragDropButton');
            dragButtons.forEach((button) => {
                button.addEventListener('dragstart', function (event) {
                    event.dataTransfer.setData('Text', this.id);
                });
            })
        },


        // 根据类型判断组件是input的type
        getInputType (type) {
            let inputType = '';
            switch (type) {
                case 'input':   
                case 'remark':
                    inputType = 'text'; 
                    break;
                case 'textarea':
                    inputType = 'textarea'; 
                    break;
                case 'inputNumber':
                case 'money':      
                    inputType = 'number'; 
                    break;
                case 'radio':   
                    inputType = 'radio'; 
                    break;
                case 'checkbox':   
                    inputType = 'checkbox'; 
                    break;
                case 'date':    
                case 'daterange': 
                    inputType = 'date'; 
                    break;
                case 'picture': 
                case 'file':   
                    inputType = 'file'; 
                    break;
                case 'refselect':
                    inputType = 'select'; 
                    break;
                default:
                    break;
            }
            return inputType;
        },
    },
    mounted: function() {
        this.handleDragstart();
        this.handleDragDrop('content');
    },
}
</script>

<style lang="less">
    .new{
        width: 1200px;
        height: 900px;
        background-color: ghostwhite;
        margin: 0 auto;

        .saveOrPreview{
            text-align: left;

            button{
                margin: 0px 20px 20px 0px;
            }
        }
        .newOuter{
            width: 100%;
            height: 800px;
            display: flex;
            justify-content: center;
            align-items: center;

            .controlLibrary{
                width: 390px;
                height: 100%;
                padding: 25px 10px 10px 10px;
                background-color: gainsboro;

                .tip{
                    text-align: center;
                }
                .dragDropButton{
                    width: 160px;
                    height: 30px;
                    margin: 10px;
                }
            }
            .mainForm{
                width: 420px;
                height: 100%;
                background: url('../assets/mobile.png') no-repeat center;
                display: flex;
                justify-content: center;
                align-items: center;

                .content{
                    width: 340px;
                    height: 650px;
                    overflow: auto;

                    /* 拖拽后的元素 */
                    .target{
                        width: 100%;
                        height: 35px;
                        background-color: #fff;
                        display: flex;
                        justify-content: space-between;
                        align-items: center;
                        flex-wrap: wrap;
                        margin-bottom: 10px;
                        line-height: 35px;
                        cursor: pointer;

                        .targetLabel{
                            width: 80px;
                            height: 100%;
                            text-align: right;
                        }
                        .targetLabelRequired::before {
                            display: inline-block;
                            margin-right: 4px;
                            color: #ff4d4f;
                            font-size: 14px;
                            font-family: SimSun, sans-serif;
                            line-height: 1;
                            content: '*';
                        }
                        .targetValue{
                            width: 220px;
                            height: 100%;
                            padding-left: 20px;
                            border-radius: 6px;
                            border: 1px solid #d9d9d9;
                        }
                        .targetValueDisabled{
                            background-color: gainsboro;
                        }
                        .closeIcon{
                            width: 20px;
                            height: 20px;
                            border-radius: 10px;
                            font-size: 14px;
                            background-color: #ff4d4f;
                            text-align: center;
                            line-height: 20px;
                            color: #fff;
                        }
                        // 金额特有
                        .hintWord{
                            margin-left: 90px;
                        }
                    }
                }
            }
            .controlSet{
                width: 390px;
                height: 100%;
                padding: 20px;
                background-color: gainsboro;
                text-align: left;

                // label{
                //     width: 100px;
                // }
                .setTitle{
                    text-align: center;
                }
                .elementType{
                    font-size: 20px;
                    font-weight: 600;
                    color: #1890ff;
                }
            }
        }
    } 

    

/* 元素属性设置 */
.controlAttribute{
    width: 100%;
    margin-bottom: 10px;
}

.topTip,
.bottomTip{
    color: gray;
}
.settingsOuter{
    width: 100%;
    display: flex;
    align-items: center;
}

input[type='text']{
    width: 100%;
    height: 35px;
    border: 1px solid gainsboro;
    border-radius: 5px;
    padding: 0px 5px;
    color: #121212;
}
input[type='checkbox']{
    width: 20px;
    height: 20px;
    border: 1px solid gainsboro;
    border-radius: 5px;
    margin-right: 5px;
}

</style>
