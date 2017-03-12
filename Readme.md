# zero-to-preact

Create .gitignore
```
/node_modules
/build
.DS_Store
```

Run
```
npm init -y
npm install --save-dev webpack webpack-dev-server babel-core babel-loader babel-preset-es2015 babel-plugin-transform-react-jsx
```

Add to package.json (inside scripts)
```
"build": "webpack",
"start": "webpack-dev-server --progress --hot --inline"
```

Run
```
npm run start
npm run build
```

