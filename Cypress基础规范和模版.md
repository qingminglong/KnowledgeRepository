Cypress基础规范和模版如下：
1、组件事件监听的模拟的模版
cy.mount(XxxComponent, {
  props: {
    pageListData: mockPageData,
    page: {
      pageNum: 1,
      pageSize: 10,
      total: 2,
      sizes: [10, 20, 50]
    },
    onSizeChange: () => __stubs.onSizeChange(params),
    onCurrentChange: __stubs.onCurrentChange(params)
  },
  global: {
    provide: {
      globalConfig: {
        appState: {
          appType: 1,
          isBusinessManager: true
        }
      },
      isFieldEnv: false
    },
    stubs: {
      'operation-button': true
    }
  }
})
const onSizeChange = cy.spy().as('onSizeChange')
const onCurrentChange = cy.spy().as('onCurrentChange')
    const stubs = {
      onSizeChange,
      onCurrentChange
    }
    window['__stubs'] = stubs
    cy.wait(0).then(() => {
      cy.get('@onSizeChange').should('have.been.calledOnce')
    })


2、接口请求和响应模拟的模版
cy.intercept('get', '**/query', async request => {
    request.reply({
      code: { code: '0000', msgId: 'RetCode.Success', msg: '操作成功' },
      bo: {
        id: 'APP0607935483742531584',
        tenantId: '10001',
        appNameCn: '渠道投诉',
        appNameEn: '渠道投诉',
        status: 'online',
        homePageUrl: '',
        logo: '',
        logoRedirectUrl: '',
        isAddWatermark: true,
        showNav: true,
        showTop: true,
        showMobileApproval: true,
        showAppletCapsule: false,
        description: '渠道投诉',
        createBy: '10279153',
        homePageId: 'PAGE0724265848802930688',
        descriptionEn: '',
        lastModifiedBy: '10239986',
        createTime: '2021-07-15 10:35:46',
        lastModifiedTime: '2023-02-27 15:39:00',
        secretKey: '',
        displayComment: true
      },
      other: null
    })
  })



// spying
cy.intercept('/users/**')
cy.intercept('GET', '/users*')
cy.intercept({
  method: 'GET',
  url: '/users*',
  hostname: 'localhost',
})
// spying and response stubbing
cy.intercept('POST', '/users*', {
  statusCode: 201,
  body: {
    name: 'Peter Pan',
  },
})
// spying, dynamic stubbing, request modification, etc.
cy.intercept('/users*', { hostname: 'localhost' }, (req) => {
  /* do something with request and/or response */
})


3、vue3组件provide模拟的模版
cy.mount(XxxComponent, {
  props: {},
  global: {
    provide: {
      globalConfig: {
        appState: {
          appType: 1,
          isBusinessManager: true
        }
      }
    }
  }
})


4、vue3内置组件模拟的模版
cy.mount(XxxComponent, {
      propsData: {},
      global: {
        stubs: {
          transition: false,
          'transition-group': false
        }
      }
    })


5、组件库和i8n的全局定义的模版
// ***********************************************************
// 这个示例支持/组件.ts在您的测试文件之前被自动处理和加载。
//
// 这是一个配置全局行为并修改Cypress的好地方。
//
// 你可以改变这个文件的位置，或者使用'supportFile'配置选项来关闭自动提供支持文件。
//
// 你可以在这里阅读更多相关信息：
// https://on.cypress.io/configuration
// ***********************************************************
// 使用ES2015语法导入commands.js：
import './commands'
// 或者使用CommonJS语法：
// require('./commands')
import VeinUIPlus from 'vein-plus'
import 'vein-plus/dist/index.css'
import { mount } from 'cypress/vue'
import { createI18n } from 'vue-i18n'
import { i18nInit } from '../../src/locale/index.ts'
const i18n = createI18n({
  legacy: false,
  locale: 'zh_CN',
  fallbackLocale: 'zh_CN'
})
i18nInit(i18n)
// 扩展Cypress命名空间以包括自定义命令的类型定义。
// 或者，可以在spec的顶部使用<reference path="./component" />来定义。
declare global {
  namespace Cypress {
    interface Chainable {
      mount: typeof mount
    }
  }
}
Cypress.Commands.add('mount', (component, options = {}) => {
  // 设置选项对象、定义全局组件
  options.global = options.global || {}
  options.global.components = options.global.components || {}
  options.global.plugins = options.global.plugins || []
  options.global.plugins.push({
    install(app) {
      app.use(VeinUIPlus)
      app.use(i18n)
    }
  })
  return mount(component, options)
})



