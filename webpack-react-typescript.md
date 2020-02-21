webpack+react+typecript脚手架基础配置

hello，我是土豆泥，下面是typescript下的hello world。

## 初始环境

- node 12.16.0

- npm v6.13.7

## 目录结构
```javascript
config
  - base.conf.js
  - dev.conf.js
  - pro.conf.js
public
  - index.html
src
  - assets
  - components
  - pages
  - App.tsx
  - index.tsx
tsconfig.json
.eslintrc.js
.babelrc
images.d.ts
```
## 初始依赖

webpack依赖

```javascript

npm i -D webpack webpack-cli webpack-merge html-webpack-plugin clean-webpack-plugin webpack-server-dev

```

react依赖

```javascript
npm i -D react react-dom @type/react @type/react-dom
```

typecript依赖

```javascript
npm i -D webpack typecript ts-loader source-map-loader tsc-init
```

eslint依赖

```javascript
 npm i -D eslint --save-dev
```

loader依赖

```javascript

npm i -D less-loader postcss-loader style-loader url-loader

```

## .eslintrc.js

```javascript

module.exports = {
    "env": {
        "browser": true,
        "es6": true,
        "node": true,
        "jest": true,
    },
    "settings": {
        "import/resolver": {
          "node": {
            "extensions": [".js", ".jsx", ".ts", ".tsx"]
          }
        }
    },
    "extends": [
        "plugin:react/recommended",
        "airbnb"
    ],
    "globals": {
        "Atomics": "readonly",
        "SharedArrayBuffer": "readonly"
    },
    "parser": "@typescript-eslint/parser",
    "parserOptions": {
        "ecmaFeatures": {
            "jsx": true
        },
        "ecmaVersion": 2018,
        "sourceType": "module"
    },
    "plugins": [
        "react",
        "@typescript-eslint"
    ],
    "rules": {
        "no-extra-semi": 2, // 禁止不必要的分号
        "quotes": ['error', 'single'], // 强制使用单引号
        "no-unused-vars": 0, // 不允许未定义的变量
        "jsx-control-statements/jsx-use-if-tag": 0,
        "no-dupe-args": 2,
        "react/prefer-stateless-function": [0],
        "import/no-extraneous-dependencies": ["error", {"devDependencies": true}],
        "linebreak-style": [0 ,"error", "windows"],
        "react/jsx-filename-extension": ["error", { "extensions": [".js", ".jsx", ".ts", ".tsx"] }]
      }
};

```

## tsconfig.json

```javascript

{
  "compilerOptions": {
    "allowUnreachableCode": true, // 不报告执行不到的代码错误。
    "allowUnusedLabels": false,	// 不报告未使用的标签错误
    "alwaysStrict": false, // 以严格模式解析并为每个源文件生成 "use strict"语句
    "baseUrl": ".", // 工作根目录
    "experimentalDecorators": true, // 启用实验性的ES装饰器
    "jsx": "react", // 在 .tsx文件里支持JSX
    "sourceMap": true, // 是否生成map文件
    "module": "commonjs", // 指定生成哪个模块系统代码
    "noImplicitAny": false, // 是否默认禁用 any
    "removeComments": true, // 是否移除注释
    "importHelpers": true,
    "rootDir": "src",
    "types": [ //指定引入的类型声明文件，默认是自动引入所有声明文件，一旦指定该选项，则会禁用自动引入，改为只引入指定的类型声明文件，如果指定空数组[]则不引用任何文件
      "node", // 引入 node 的类型声明
    ],
    "paths": {
      "src/*":[
        "src/*"
      ]
    },
    "moduleResolution": "node",
    "target": "ESNext", // 编译的目标是什么版本的
    "outDir": "build/dist", // 输出目录
    "lib": [ // 编译过程中  需要引入的库文件的列表
      "es5",
      "es2015",
      "es2016",
      "es2017",
      "es2018",
      "dom"
    ]
  },
  "include": [
    "src/**/*",
    "images.d.ts"
  ],
  // 指定一个排除列表（include的反向操作）
  "exclude": [
    "node_modules",
    "build",
  ],
}
```

## postcss.config.js

```javascript

const autoprefixer = require('autoprefixer');

module.exports = {
  plugins: [
    autoprefixer({ browsers: ['last 2 versions'] }),
  ],
};

```

## images.d.ts
```javascript

declare module '*.svg'
declare module '*.png'
declare module '*.jpg'
declare module '*.jpeg'
declare module '*.gif'
declare module '*.bmp'
declare module '*.tiff'

```

## .babelrc

```javascript


{
  "presets": [
      [
          "@babel/preset-env",   // 将ES6语法转换为es5
          {
              "useBuiltIns": "usage",    // 只编译需要编译的代码
              "corejs": "3.0.1",
          }
      ],
      [
        "@babel/preset-react",
        {
          "pragma": "dom", // default pragma is React.createElement
          "pragmaFrag": "DomFrag", // default is React.Fragment
          "throwIfNamespace": false // defaults to true
        }
      ]
    ],
    "plugins": ["@babel/plugin-transform-react-jsx"]
}


```


## 配置base.config.js


```javascript

const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

const template = path.resolve(__dirname, '../public/index.html');
const env = process.env.NODE_ENV;
const DIST_PATH = path.resolve(__dirname, '../build');


module.exports = {
  entry: {
    app: './src/index.tsx',
  },
  output: {
    path: DIST_PATH,
  },
  resolve: {
    extensions: ['.ts', '.tsx', '.js', '.jsx', 'json'],
    alias: {
      src: path.resolve(__dirname, '../src/'),
    },
  },
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        loader: 'ts-loader',
        exclude: /node_modules/,
      },
      {
        enforce: 'pre',
        test: /\.js$/,
        loader: 'source-map-loader',
      },
      {
        test: /\.jsx?$/, // ?表示x有0个或一个
        exclude: /(node_modules|bower_components)/,
        include: path.resolve(__dirname, '../src'), // 只在include包含的目录下进行loader编译
        use: {
          loader: 'babel-loader',
        },
      },
      // 加载解析文件资源
      {
        test: /\.(jpe?g|png|gif)$/,
        use: {
          loader: 'url-loader', // 和file-loader功能相同，但更智能
          options: {
            esModule: false, // 这里设置为false, 默认是true，打包出来的图片src=[object Module]
            // 配置打包后的文件名,具体可看webpack的file-loader文档
            name: '[name].[ext]?[hash]',
            outputPath: 'images/',
            limit: 4096, // 当图片大小大于4k时将以文件形式输出，否则以base64输出
          },
        },
      },
      // 引入字体，svg等文件
      {
        test: /\.(eot|ttf|svg)$/,
        use: {
          loader: 'file-loader',
        },
      },
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({ // 向dist文件中自动添加模版html
      template,
      filename: 'index.html',
    }),
  ],
  optimization: {
    splitChunks: {
      chunks: 'all',
    },
    usedExports: true,
  },
};


```
## 配置dev.conf.js

```javascript

const merge = require('webpack-merge');
const path = require('path');
const webpack = require('webpack');

const DIST_PATH = path.resolve(__dirname, '../build');
const baseConf = require('./base.conf.js');

const config = {
  mode: 'development',
  devtool: 'cheap-module-eval-source-map', // 开发环境配置最佳实践
  output: {
    filename: '[name].js',
    chunkFilename: '[name].js',
  },
  // 开发服务器
  devServer: {
    port: '8081',
    hot: true, // 热更新
    open: true, // 自动打开浏览器
    contentBase: DIST_PATH, // 告诉服务器从哪个目录中提供内容，只有在你需要提供静态文件时才需要
    historyApiFallback: true, // 所有404都连接到index.html
  },
  module: {
    rules: [{
      test: /\.(css|less)$/,
      use: [ // loader解析的顺序是从下到上，从右到左的顺序
        'style-loader', // 使用MiniCssExtractPlugin时就不能使用style-loader了
        {
          loader: 'css-loader',
          options: {
            importLoaders: 2, // 该方式可以让@import引入的css文件再次执行一边css打包loader
          },
        },
        'postcss-loader',
        'less-loader',
      ],
    }, // 加载解析文件资源
    ],
  },
  plugins: [
    new webpack.HotModuleReplacementPlugin(),
  ],
};

module.exports = merge(config, baseConf);

```

## 配置pro.conf.js

```javascript

const merge = require('webpack-merge');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const baseConf = require('./base.conf.js');

const config = {
  mode: 'production',
  devtool: 'cheap-module-source-map', // 生产配置最佳实践
  output: {
    filename: 'js/[name]_[contenthash].js', // 入口和内容hash组成的文件名，也可以是hash
    chunkFilename: 'js/[name]_[contenthash].chunk.js',
  },
  module: {
    rules: [
      {
        test: /\.(css|less)$/,
        use: [ // loader解析的顺序是从下到上，从右到左的顺序
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              filename: '[name]_[contenthash:8].css',
              chunkFilename: '[name]_[contenthash:8].css',
              publicPath: '../', //* ***最后打包的时候替换引入文件路径
            },
          },
          // 'style-loader',  使用MiniCssExtractPlugin时就不能使用style-loader了
          {
            loader: 'css-loader',
            options: {
              importLoaders: 2, // 该方式可以让@import引入的css文件再次执行一边css打包loader
            },
          },
          'postcss-loader',
          'less-loader',
        ],
      },
    ],
  },
  plugins: [
    new MiniCssExtractPlugin({
      // Options similar to the same options in webpackOptions.output
      // both options are optional
      filename: 'css/[name]_[hash].css',
      chunkFilename: 'css/[name]_[hash].chunk.css',
    }),
    new CleanWebpackPlugin(), // 打包后先清除dist文件，先于HtmlWebpackPlugin运行
  ],
};

module.exports = merge(config, baseConf);

```

## 编写首页代码
App.tsx

```javascript
import * as React from 'react';
import * as pic from 'src/assets/img/2.jpeg'
import 'src/assets/css/app.less'

export default class App extends React.Component {
  render() {
   const style = {
     color: 'red'
   }
    return (
      <div className="app">
        <div style={style} className="test">hello world</div>
        <img src={pic}></img>
      </div>
    );
  }
}

```

index.tsx

```javascipt

import * as React from "react";
import * as ReactDOM from "react-dom";

import App from "./App";

ReactDOM.render(
  <App/>,
  document.getElementById('root')
);

```

## 修改package.json

```javascript

     "start": "webpack --config ./config/dev.conf.js",
    "build": "webpack --config ./config/pro.conf.js",
    "dev": "webpack-dev-server --config ./config/dev.conf.js"

```







