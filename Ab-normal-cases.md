## Jest encountered an unexpected token

      D:\scp.net_front\scpnet-front\node_modules\lodash-es\lodash.js:10
      export { default as add } from './add.js';
      ^^^^^^

      SyntaxError: Unexpected token 'export'

### Solution: insert to `jest.config.js`
      
      "moduleNameMapper": {
        "^lodash-es$": "lodash"
      }
