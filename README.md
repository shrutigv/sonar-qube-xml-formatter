Sample karma.config.js

```xml
'use strict';

var path = require('path');
var conf = require('./gulp/conf');

var _ = require('lodash');
var wiredep = require('wiredep');

var pathSrcHtml = [
  path.join(conf.paths.src, '/**/*.html'),
  path.join(conf.paths.src_test, '/**/*.html')
];

function listFiles() {
  var wiredepOptions = _.extend({}, conf.wiredep, {
    dependencies: true,
    devDependencies: true
  });

  return wiredep(wiredepOptions).js
    .concat([
      path.join(conf.paths.src, '/app/**/*.module.js'),
      path.join(conf.paths.src, '/app/**/*.js'),
      path.join(conf.paths.src, '/**/*.spec.js'),
      path.join(conf.paths.src, '/**/*.mock.js'),
      path.join(conf.paths.src_test, '/app/**/*.module.js'),
      path.join(conf.paths.src_test, '/app/**/*.js'),
      path.join(conf.paths.src_test, '/**/*.spec.js'),
      path.join(conf.paths.src_test, '/**/*.mock.js')
    ])
    .concat(pathSrcHtml);
}

process.env.CHROME_BIN = require('puppeteer').executablePath();

module.exports = function(config) {
  config.set({

    // base path that will be used to resolve all patterns (eg. files, exclude)
    basePath: '',
    plugins: [
      require('karma-jasmine'),
      require('karma-chrome-launcher'),
      require('karma-jasmine-html-reporter'),
      require('karma-coverage'),
      require('karma-typescript'),
      require('sonarqube-unit-test-reporter')
  ],

    // frameworks to use
    // available frameworks: https://npmjs.org/browse/keyword/karma-adapter
    frameworks: ['jasmine','karma-typescript'],

    // list of files / patterns to load in the browser
    files: [
      {pattern: './src/helloComponent/helloComponent.spec.ts', watched: false},
      {pattern:'./src/helloComponent/helloComponent.ts', watched: false}
    ],

    // list of files to exclude
    exclude: [
      'node_modules',
      ],
    include: [
        './src/helloComponent/helloComponent.spec.ts',
        './src/helloComponent/helloComponent.ts'
    ],

    // preprocess matching files before serving them to the browser
    // available preprocessors: https://npmjs.org/browse/keyword/karma-preprocessor

    preprocessors: {
      "./src/helloComponent/helloComponent.spec.ts" : ["karma-typescript", "coverage"],
      "./src/helloComponent/helloComponent.ts" : ["karma-typescript"]
    },
    
    //typescript transpiler configuration
     karmaTypescriptConfig: {
      reports:
      {
        "lcovonly": {
          "directory": "./.tmp/sonarQube/coverage/",
          "filename": "lcov.info",
        }
          
      },
      bundlerOptions: {
        transforms: [
            require("karma-typescript-es6-transform")()
          ]
        },
        transformPath: function(filepath) {
            return filepath.replace(/\.(ts|tsx)$/, ".js");
        },
        
      exclude: ['node_modules'],
      include: [
        './src/helloComponent/helloComponent.spec.ts',
        './src/helloComponent/helloComponent.ts'
        
    ],
      compilerOptions: {
        "module": "commonjs",
        "moduleResolution": "node",
        "noImplicitAny": true,
        "outDir": ".tmp",
        "preserveConstEnums": true,
        "removeComments": true,
        "sourceMap": true,        
        "experimentalDecorators": true,
        "emitDecoratorMetadata": true,  
        "target": "es2015",
        "allowSyntheticDefaultImports": true,
      },
    },

   //webpack: webpackConfig,

  //Hide the webpack compilation noise

    webpackMiddleware: {
      noInfo: true
    },

    // test results reporter to use
    // possible values: 'dots', 'progress'
    // available reporters: https://npmjs.org/browse/keyword/karma-reporter
    reporters: ['progress', 'coverage', 'sonarqubeUnitTest'],

    sonarQubeUnitTestReporter: {
      sonarQubeVersion: 'LATEST',
      outputFile: './.tmp/sonarQube/coverage/test-report.xml',
      useBrowserName: false,
      testPaths: ['./src/helloComponent'],
      testFilePattern: '.spec.ts'
    },
    

    // optionally, configure the reporter
    coverageReporter: {
      type : 'lcov',
      dir : './.tmp/sonarQube',
      subdir : 'coverage',
      check: {
        global: {
          statements: 75,
          branches: 50,
          functions: 100,
          lines: 10
        },
        each: {
          statements: 75,
          branches: 50,
          functions: 100,
          lines: 10,
        }
      },
      watermarks: {
        statements: [ 50, 75 ],
        functions: [ 50, 100 ],
        branches: [ 25, 50 ],
        lines: [ 10, 55 ]
      }
    },
       
    

    // web server port
    port: 9876,

    // enable / disable colors in the output (reporters and logs)
    colors: true,

    // start a new run immediately when a change is detected.
    restartOnFileChange:true,

    // level of logging
    // possible values: config.LOG_DISABLE || config.LOG_ERROR || config.LOG_WARN || config.LOG_INFO || config.LOG_DEBUG
    logLevel: config.LOG_ERROR,

    // enable / disable watching file and executing tests whenever any file changes
    autoWatch: true,

    // start these browsers
    // available browser launchers: https://npmjs.org/browse/keyword/karma-launcher
    //browsers: ['Chrome'],
    browsers: ['ChromeHeadless'],

    // Continuous Integration mode
    // if true, Karma captures browsers, runs the tests and exits
    singleRun: true,
    // leave Jasmine Spec Runner output visible in browser
    client:{
      clearContext: false 
    },

    // Concurrency level
    // how many browser should be started simultaneous
    concurrency: 1
  });
}

```

##### Prerequisites for development

* NodeJS 8.0.0 https://nodejs.org/download/release/v8.0.0/

##### Build

* npm install
* npm build

## Example

https://github.com/shrutigv/sonarQube-typescript-setup