# lektor-webpack-html-helper

This is a plugin for Lektor that adds support for generating templates with webpacks HtmlWebpackPlugin. These templates can be generated into the assets folder which will be observed for newly created or modified html files.
These files will then be copied over to Lektors template folder so that they can be used by Lektor.
This plugin depends on the [lektor-webpack-support](https://github.com/lektor/lektor-webpack-support) plugin to be really useful.

# webpack/webpack.config.js
```js
const path = require("path");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const { CleanWebpackPlugin } = require("clean-webpack-plugin");


module.exports = {
    mode: "production",
    entry: {
        main: "./src/index.js"
    },
    output: {
        filename: "[name].bundle.js",
        path: path.dirname(__dirname) + "/assets/generated" 
    },
    optimization: {
        minimizer: [
            new HtmlWebpackPlugin({
                inject: false,
                filename: "layout_generated.html",
                template: "./src/layout_template.html"
            })
        ],
    },
    plugins: [
        new CleanWebpackPlugin(),
        new MiniCssExtractPlugin({
            filename: "[name].css"
        })
    ],
    module: {
        rules: [{
            test: /\.scss$/,
            use: [
                MiniCssExtractPlugin.loader,
                "css-loader",
                "sass-loader"
            ]
        }]
    }
}
```

# webpack/src/layout_template.html
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>{{ this.title }} &middot; {{ config.PROJECT.name }} </title>

    <% for (var css in htmlWebpackPlugin.files.css) { %>
    <link href="{{ '/generated/<%= htmlWebpackPlugin.files.css[css] %>' | asseturl }}" rel="stylesheet">
    <% } %>
</head>

<body>
    <main>
        {% block content %}
        {% endblock content %}
    </main>

    <% for (var chunk in htmlWebpackPlugin.files.chunks) { %>
    <script src="{{ '/generated/<%= htmlWebpackPlugin.files.chunks[chunk].entry %>' | asseturl }}"></script>
    <% } %>
</body>

</html>
```