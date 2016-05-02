## Setting up Angular with Protractor and Karma testing

* `node -v` **-->** checks what version of node you have.  

* If none is found then run `brew install node`.  
* `npm init` **-->** Sets up the `package.json` file, choose all of the default settings.  

* `npm install bower -g --save-dev` **-->** Installs bower and sets up the `node modules` directory.

* `bower init` **-->** Creates a `bower.json` file, choose all of the default settings.  

* `mkdir app`
* `touch .bowerrc` **-->** Make it in the ***root directory***!
  - In the `.bowerrc` file put this text in:
  ```Javascript
  {
  "directory": "app/bower_components"
  }
```
* `bower install angular --save` **-->** Installs angular using bower. Sets up the `bower_components` folder in `app`.

* `touch .gitignore` and add this to the file:
  ```
  mode_modules
  app/bower_components
  ```
---
### Setting up Protractor:
* Make sure [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/index.html) is installed.  

* `java -version` **-->** check what version you have.  

* `npm install -g protractor` **-->** installs Protractor globally and `webdriver-manager`.  

* Run `protractor --version` to check if it is working.  

* `webdriver-manager update` **-->** Downloads the necessary binaries.  

* `webdriver-manager start` **-->** Starts the server.  

* `http://localhost:4444/wd/hub` **-->** Shows you information about the status of the server.  

* `npm install --save-dev protractor` **-->** Adds it to the list of dependencies.

* `mkdir test`  

* `touch test/protractor.conf.js` and add to the file:
  ```Javascript
    exports.config = {
      seleniumAddress: 'http://localhost:4444/wd/hub',
      specs: ['e2e/*.js'],
      baseUrl: 'http://localhost:8080'
    }
  ```

* `npm install --save http-server`  

* `npm install http-server -g`  

* `http-server` **-->** Starts http-server.

##### Adding colour and format to the *protractor* tests:
* `npm install jasmine-spec-reporter --save-dev`

* Add to `protractor.conf.js` inside the `exports.config {}` hash:
  ```Javascript
    onPrepare: function() {
      var SpecReporter = require('jasmine-spec-reporter');
      jasmine.getEnv().addReporter(new SpecReporter({displayStacktrace: 'all'}));
     }
  ```
* **More info at:**
  - [https://github.com/bcaudan/jasmine-spec-reporter/blob/master/README.md](https://github.com/bcaudan/jasmine-spec-reporter/blob/master/README.md)
  - [https://github.com/bcaudan/jasmine-spec-reporter/blob/master/docs/protractor-configuration.md](https://github.com/bcaudan/jasmine-spec-reporter/blob/master/docs/protractor-configuration.md)

---

### Setting up Karma
* `npm install karma --save-dev`  

* `npm install karma-jasmine karma-chrome-launcher --save-dev`  

* `npm install jasmine-core --save-dev`  

* `npm install -g karma-cli`
* `bower install angular-mocks --save-dev`

* Either run `karma init` ***OR***,
* Run `touch test/karma.conf.js` and put in the file:
  ```Javascript
    module.exports = function(config){
      config.set({

        basePath : '../',

        files : [
          'app/bower_components/angular/angular.js',
          'app/bower_components/angular-mocks/angular-mocks.js',
          'app/js/**/*.js',
          'test/unit/**/*.js'
        ],

        autoWatch : true,

        frameworks: ['jasmine'],

        browsers : ['Chrome'],

        plugins : [
                'karma-chrome-launcher',
                'karma-jasmine'
        ]
      });
  };
  ```

##### Adding colour and format to the *karma* tests:
* `npm install karma-spec-reporter --save-dev`

* Add to `karma.config.js` inside `config.set ({})` underneath `plugins`:
    ```Javascript
    reporters: ["spec"],
      specReporter: {
        maxLogLines: 5,         
        suppressErrorSummary: true,  
        suppressFailed: false,  
        suppressPassed: false,  
        suppressSkipped: true,  
        showSpecTiming: false
      },
    ```
* Add to the `plugins` array:
    ```Javascript
      "karma-spec-reporter"
    ```
* **More info at:** [https://github.com/mlex/karma-spec-reporter](https://github.com/mlex/karma-spec-reporter)


* `karma start test/karma.conf.js` **-->** Runs the tests.

---

### Setting up protractor-http-mock
* `npm install protractor-http-mock --save-dev`  

* Add to `protractor.conf.js` inside `exports.config = {}` underneath `baseUrl`:
  ```Javascript
  onPrepare: function(){
    require('protractor-http-mock').config = {
      rootDirectory: process.cwd(),
      protractorConfig: 'test/protractor.conf.js'
    };
  }
  ```

* Update any feature tests to require the library, *example taken from ToDo list*:
  ```Javascript
  var mock = require('protractor-http-mock');

  beforeEach(function(){
    mock([{
      request: {
        path: 'http://quiet-beach-24792.herokuapp.com/todos.json',
        method: 'GET'
      },
      response: {
        data: [{text: "ToDo1", completed: true}, {text: "ToDo2", completed: false}]
      }
    }]);
  });

  afterEach(function(){
    mock.teardown();
  });
  ```
