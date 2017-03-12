# zero-to-preact

create .gitignore
```
/node_modules
/build
.DS_Store
```

```
npm init -y
npm install --save-dev webpack webpack-dev-server babel-core babel-loader babel-preset-es2015 babel-plugin-transform-react-jsx
```

add to package.json (inside scripts)
```
"build": "webpack",
"start": "webpack-dev-server --progress --hot --inline"
```

