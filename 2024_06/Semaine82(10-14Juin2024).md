**10 Juin**
veille payload too large error
résolution de tests failing : Jest encountered an unexpected token  
mistral : 
```
The error you're encountering is because Jest is not configured to handle ES6 module imports/exports in your project. By default, Jest only understands CommonJS syntax, so you need to set up Babel to transpile your code to CommonJS before Jest runs it.

Here are the steps to configure Jest with Babel:

Install the necessary dependencies:

npm install --save-dev babel-jest @babel/core @babel/preset-env
Create a babel.config.json file in the root of your project with the following content:

{
  "presets": [
    ["@babel/preset-env", { "targets": { "node": "current" } }}]
  ]
}
Update your jest.config.js file to include the following:

module.exports = {
  // ...
  transform: {
    '^.+\\.jsx?$': 'babel-jest',
  },
  // ...
};
If you don't have a jest.config.js file, you can create one in the root of your project.

Restart your test runner.
With these configurations, Jest will use Babel to transpile your code before running the tests, and the import/export syntax should work as expected.
```

rajouter la lgine dans le package json : 
```
  "transformIgnorePatterns": [
    "node_modules/(?!axios)"
  ],
```
résout le problème et arrive à : 
```
Test suite failed to run                          
TypeError: _utils.default.inherits is not a function
```

