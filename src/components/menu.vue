<template>
  <div>
    <el-row class="menu_setting_area">
      <el-col :span="12">
        <div class="menu_preview_area">
          <div class="mobile-bd">
            <el-row class="menulist">
              <el-col v-for="(item,index) in menulist" :key="index" :span="menulist.length<2?12:8" class="menuitem">
                <a class="pre_menu_link" :style="first_selected===index&&!second_selected?'border:1px solid #44b549':''"
                   @click="changeSelect(index)" href="javascript:">{{item.name}}</a>
                <ul v-show="first_selected===index" class="sub_pre_menu_box">
                  <li v-for="(i,x) in sub_menulist" :key="x"
                      :style="second_selected===x+1?'border:1px solid #44b549':''" @click="second_selected=x+1">
                    <span>{{i.name}}</span>
                  </li>
                  <li @click="add_secondmenu(index)" v-show="!sub_menulist||sub_menulist.length<5"><a
                    class="el-icon-plus"></a></li>
                </ul>
              </el-col>
              <el-col v-show="menulist.length<3" :span="menulist.length<2?12:8" class="menuitem"><a
                class="pre_menu_link" @click="addmenu" href="javascript:"><span class="el-icon-plus"></span></a>
              </el-col>
            </el-row>
          </div>
        </div>
      </el-col>
      <el-col :span="12">
        <div v-show="menulist.length" class="menu_form_area">
          <div id="js_rightBox">
            <div class="editor_inner">
              <div class="menu_form_hd">
                <h4 style="float:left;font-weight: 400;margin:0">
                  {{second_selected ? menulist[first_selected].sub_button[second_selected - 1].name : menulist[first_selected].name}}</h4>
                <div style="text-align: right">
                  <a href="javascript:" @click="delmenu">删除菜单</a>
                </div>
              </div>
              <div class="menu_form_bd">
                <el-form style="margin-top: 30px" label-position="left" label-width="80px">
                  <el-form-item label="菜单名称">
                    <el-input size="small" type="text" @change="changename"
                              v-model="!second_selected?menulist[first_selected].name:menulist[first_selected].sub_button[second_selected-1].name"
                              auto-complete="off"></el-input>
                    <span>{{second_selected ? '字数不超过八个汉字或16个字母' : '字数不超过四个汉字或8个字母'}}</span>
                  </el-form-item>
                  <div
                    v-show="second_selected||!menulist[first_selected].sub_button||!menulist[first_selected].sub_button.length">
                    <el-form-item label="菜单内容">
                      <el-radio v-model="type" label='click'>发送消息</el-radio>
                      <el-radio v-model="type" label='view'>跳转网页</el-radio>
                      <el-radio label="3">跳转小程序</el-radio>
                    </el-form-item>
                    <div class="msg_tab">
                      <el-form-item style="padding:10px" v-show="this.type==='view'" label="链接地址">
                        <el-input type="text" v-model="url"></el-input>
                      </el-form-item>
                      <div v-show="this.type==='click'">
                        <el-row class="tab_navs_panel">
                          <el-col v-for="(item,index) in msgtypelist" :key="index" :span="4" :offset="index?1:0"><a
                            @click="changemsg(item)" :style="msgtype===item.value?'color:#222222':''"
                            href="javascript:">{{item.name}}</a></el-col>
                        </el-row>
                        <el-form-item style="padding:10px" v-show="this.msgtype==='text'" label="消息内容">
                          <el-input type="textarea" v-model="textmsg" :autosize="{ minRows: 4, maxRows:8}"></el-input>
                        </el-form-item>
                        <div v-show="this.msgtype!=='text'" style="padding:14px 20px;border-top:1px solid #e7e7eb">
                          <el-row style="padding:20px">
                            <el-col class="create_access" :span="10">
                              <span style="padding: 10px" class="el-icon-plus"></span><br>
                              <a href="">从素材库选择</a>
                            </el-col>
                            <el-col :offset="4" class="create_access" :span="10">
                              <span style="padding: 10px" class="el-icon-plus"></span><br>
                              <a href="">新建{{typename}}</a>
                            </el-col>
                          </el-row>
                        </div>
                      </div>
                    </div>
                  </div>
                </el-form>
              </div>
            </div>
          </div>
        </div>
      </el-col>
    </el-row>
    <div style="margin-top: 40px;text-align: center;padding-bottom:50px;">
      <el-button type="success" @click="submit">保存并发布</el-button>
      <el-button>预览</el-button>
    </div>
  </div>
</template>

<script>
  import ElRow from 'element-ui/packages/row/src/row'
  export default {
    components: {ElRow},
    name: 'menuSetting',
    data() {
      return {
        first_selected: 0,
        second_selected: '',
        sub_menulist: '',
        type: 'click',
        msgtype: 'text',
        typename: '',
        textmsg: '',
        url: '',
        msgtypelist: [{'name': '文本消息', 'value': 'text'}, {'name': '图文消息', 'value': 'imgtext'}, {
          'name': '图片',
          'value': 'img'
        }, {'name': '语音', 'value': 'voice'}, {'name': '视频', 'value': 'video'}],
        menulist: [
          {'type': 'click', 'name': '菜单名称', 'msgtype': 'text'},
          {'type': 'click', 'name': '菜单名称', 'msgtype': 'text'},
          {'type': 'click', 'name': '菜单名称', 'msgtype': 'text'}
        ]
      }
    },
    watch: {
      second_selected() {
        this.setContent()
      },
      first_selected() {
        this.setContent()
      },
      type(val) {
        this.changeContent('type', val)
        if (val === 'click') {
          this.changeContent('msgtype', 'text')
        } else {
          this.changeContent('msgtype', '')
        }
      },
      url(val) {
        this.changeContent('url', val)
      },
      textmsg(val) {
        this.changeContent('textmsg', val)
      }
    },
    methods: {
      changeContent(name, value) {
        if (this.second_selected) {
          this.menulist[this.first_selected].sub_button[this.second_selected - 1][name] = value
        } else {
          this.menulist[this.first_selected][name] = value
        }
      },
      setContent() {
        var contentlist = ['type', 'msgtype', 'textmsg', 'url']
        contentlist.map(ii => {
          if (this.second_selected) {
            this[ii] = this.menulist[this.first_selected].sub_button[this.second_selected - 1][ii]
          } else {
            this[ii] = this.menulist[this.first_selected][ii]
          }
        })
      },
      addmenu() {
        this.menulist.push({type: 'click', name: '菜单名称', msgtype: 'text'})
      },
      changeSelect(index) {
        this.first_selected = index
        this.second_selected = ''
        this.sub_menulist = this.menulist[index].sub_button || ''
      },
      add_secondmenu(index) {
        if (!this.menulist[index].sub_button || this.menulist[index].sub_button.length < 5) {
          if (!this.menulist[index].sub_button) {
            this.menulist[index].sub_button = []
          }
          this.menulist[index].sub_button.push({'type': 'click', 'name': '子菜单名称', msgtype: 'text'})
          this.sub_menulist = this.menulist[index].sub_button
          this.second_selected = this.sub_menulist.length
        }
      },
      delmenu() {
        var sub, index
        if (this.menulist.length === 1) {
          alert('最少一个菜单')
          return
        }
        if (this.second_selected) {
          sub = this.menulist[this.first_selected].sub_button[this.second_selected - 1]
          index = this.menulist[this.first_selected].sub_button.indexOf(sub)
          this.menulist[this.first_selected].sub_button.splice(index, 1)
          this.second_selected = ''
        } else {
          sub = this.menulist[this.first_selected]
          index = this.menulist.indexOf(sub)
          this.menulist.splice(index, 1)
          this.first_selected = 0
          this.sub_menulist = this.menulist[this.first_selected].sub_button || ''
        }
      },
      changename(v) {
        var length = this.second_selected ? 8 : 4
        if (v.length > length) return
        if (!v.length) return
        this.changeContent('name', v)
      },
      changemsg(type) {
        this.msgtype = type.value
        this.typename = type.name
        this.changeContent('msgtype', type.value)
      },
      submit() {
        this.deleteEl(this.menulist)
        console.log(this.menulist)
      },
      deleteEl(el) {
        el.map((ii, index) => {
          if (ii.sub_button) {
            this.menulist[index] = {name: ii.name, sub_button: ii.sub_button}
            this.deleteEl(ii.sub_button)
          }
          for (var i in ii) {
            if (ii[i] === '' || ii[i] === undefined) {
              delete ii[i]
            }
          }
        })
      }
    }
  }
</script>

<style>
  .menu_setting_area {
    width: 1000px;
    margin: 0 auto;
    padding: 20px;
    color: #353535;
    font-size: 14px;
  }
  .menu_setting_area a{
    text-decoration: none;
    color: #8d8d8d;
  }
  .menu_setting_area li, .menu_setting_area ul {
    list-style-type: none;
    margin: 0;
    text-align: center;
  }

  .menu_preview_area {
    width: 294px;
    height: 580px;
    border: solid 1px #e7e7eb;
    position: relative;
    margin: 0 auto;
    background: transparent url(../assets/bg_mobile_head.png) no-repeat 0 0 / 100%;
  }

  .menulist {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    margin: 0;
    border-top: 1px solid #e7e7eb;
    background: transparent url(../assets/bg_mobile_foot.png) no-repeat 0 0;
    padding-left: 43px;
  }

  .menuitem {
    line-height: 50px;
    position: relative;
    text-align: center;
  }

  .pre_menu_link {
    display: block;
    border-left: 1px solid #e7e7eb;
    width: auto;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    word-wrap: normal;
    color: #616161;
    text-decoration: none;
  }

  .sub_pre_menu_box {
    position: absolute;
    left: 0;
    width: 100%;
    border: 1px solid #d0d0d0;
    bottom: 60px;
    padding: 0;
    background-color: #fafafa;
  }

  .sub_pre_menu_box li {
    border-top: 1px solid #e7e7eb;
    cursor: pointer;
  }

  .sub_pre_menu_box li:first-child {
    border: none;
  }

  .menu_form_area {
    /*display: table-cell;*/
    vertical-align: top;
    height: 580px;
    /*width: 460px;*/
    float: none;
    background: #f4f5f9;
    border: 1px solid #e7e7eb;
    padding: 0 20px 5px
  }

  .editor_inner {
    min-height: 560px;
    padding-bottom: 20px;
  }

  .menu_form_hd {
    padding: 9px 0;
    border-bottom: 1px solid #e7e7eb
  }

  .msg_tab {
    background: #fff;
    border: 1px solid #e7e7eb
  }

  .create_access {
    padding: 42px 0;
    border: 2px dotted #d9dadc;
    text-align: center;
  }

  .create_access a {
    vertical-align: middle;
    margin-left: 10px;
    margin-right: 10px;
    color: #8d8d8d;
    font-size: 14px;
    line-height: normal;
  }

  .tab_navs_panel {
    height: 38px;
    line-height: 38px;
    text-align: center;
    color: #8d8d8d;
  }
</style>

