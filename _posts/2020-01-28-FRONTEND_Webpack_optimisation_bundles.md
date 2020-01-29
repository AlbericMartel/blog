---
layout: post
title:  "Optimisation des bundles webpack"
categories: Frontend
tags: Webpack
author: Albéric Martel
description: Optimisation des bundles webpack
---

# Optimisation des bundles JS générés par Webpack

_Exemple de conf webpack de prod_

    const webpack = require('webpack');
    const path = require('path');
    const bundlePath = '/web/dist/';
    const VueLoaderPlugin = require('vue-loader/lib/plugin');
    const { CleanWebpackPlugin } = require('clean-webpack-plugin');
    const WebpackAssetsManifest = require('webpack-assets-manifest');
    const MiniCssExtractPlugin = require('mini-css-extract-plugin');
    const OptimizeCssAssetsPlugin = require('optimize-css-assets-webpack-plugin');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    const HtmlWebpackHarddiskPlugin = require('html-webpack-harddisk-plugin');
    
    function resolve(dir) {
        return path.join(__dirname, dir)
    }
    
    module.exports = {
        entry: {
            'monappli-bundle': path.resolve(__dirname, 'src/main/webapp/vue/monappli/mainVue.es6'),
        },
        output: {
            path: __dirname + "/src/main/webapp" + bundlePath,
            filename: '[name].[chunkhash:8].js'
        },
        mode: 'production',
        resolve: {
            extensions: ['.js', '.es6', '.vue', '.json'],
            alias: {
                'vue$': 'vue/dist/vue.esm.js',
                '@': resolve('src')
            },
            modules: [
                path.resolve(__dirname, 'src/main/webapp/vue'),
                './node_modules'
            ]
        },
        module: {
            rules: [
                {
                    test: /\.vue$/,
                    loader: 'vue-loader'
                },
                {
                    test: /\.(js|es6)$/,
                    loader: 'babel-loader',
                    exclude: file => (
                        /node_modules/.test(file) &&
                        !/\.vue\.js/.test(file) &&
                        !/element-ui\/src/.test(file)
                    )
                },
                {
                    test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/,
                    loader: 'url-loader',
                    options: {
                        limit: 10000,
                        name: 'fonts/[name].[ext]?[hash]',
                        publicPath: bundlePath
                    }
                },
                {
                    test: /\.(svg)(\?v=[0-9]\.[0-9]\.[0-9])?$/,
                    loader: "file-loader",
                    options: {
                        publicPath: bundlePath
                    }
                },
                {
                    test: /\.css$/,
                    use: [
                        MiniCssExtractPlugin.loader,
                        "css-loader" // translates CSS into CommonJS
                    ]
                },
                {
                    test: /\.scss$/,
                    use: [
                        MiniCssExtractPlugin.loader,
                        "css-loader", // translates CSS into CommonJS
                        "sass-loader" // compiles Sass to CSS
                    ]
                }
            ]
        },
        resolveLoader: {
            alias: {
                'scss-loader': 'sass-loader'
            }
        },
        plugins: [
            new CleanWebpackPlugin(),
            new webpack.NormalModuleReplacementPlugin(/element-ui[\/\\]lib[\/\\]locale[\/\\]lang[\/\\]zh-CN/, 'element-ui/lib/locale/lang/fr'),
            new VueLoaderPlugin(),
            new MiniCssExtractPlugin({ filename: '[name].[contenthash:8].css' }),
            new OptimizeCssAssetsPlugin({
                cssProcessor: require('cssnano'),
                cssProcessorPluginOptions: {
                    preset: ['default', {normalizeWhitespace: false }]
                }
            }),
            new WebpackAssetsManifest({
                entrypoints: true, entrypointsKey: 'entryPoints',
                fileExtRegex: /\.(?!map|gz)\w+$/i,
                publicPath: true
            }),
            new HtmlWebpackPlugin({
                filename: 'auth-imports.html',
                chunks: ['runtime~monappli-bundle', 'vendors', 'monappli-bundle'],
                template: 'templates/monappli-imports.html',
                inject: false,
                alwaysWriteToDisk: true
            }),
            new HtmlWebpackHarddiskPlugin({
                outputPath: path.resolve(__dirname, 'RELATIVE_PATH')
            })
        ],
        optimization: {
            runtimeChunk: true,
            splitChunks: {
                cacheGroups: {
                    defaultVendors: {
                        test: /[\\/]node_modules[\\/]/,
                        name: 'vendors',
                        chunks: 'all',
                    }
                }
            },
        }
    };
    
Les plugins suivants sont utilisés :

- CleanWebpackPlugin : Nettoyage du répertoire dist
- MiniCssExtractPlugin : Extraction du CSS et inclusion du hash dans le nom du fichier CSS
- OptimizeCssAssetsPlugin : Minification du CSS
- WebpackAssetsManifest : Création du manifest.json. Notamment utilisable si le backend a le rôle de renvoyer les pages au front.
Il contient les infos sur les JS et CSS composant les bundles
- HtmlWebpackPlugin : Génère un fichier html contenant les imports. On peut créer un template personnalisé.
- HtmlWebpackHarddiskPlugin : Ecrit le fichier html généré sur disque

_Exemple de template HTML. Dans le résultat final ne seront présents que les imports css et js sans aucun tag head ou body_

    <% for (var css in htmlWebpackPlugin.files.css) { %>
    <link href="/web/dist/<%= htmlWebpackPlugin.files.css[css] %>" rel="stylesheet">
    <% } %>
    <% for (var chunk in htmlWebpackPlugin.files.chunks) { %>
    <script type="text/javascript" src="/web/dist/<%= htmlWebpackPlugin.files.chunks[chunk].entry %>"></script>
    <% } %>

La partie optimization est utilisée pour les parties suivantes :

- "runtimeChunk: true" indique d'extraire les runtime de chaque bundle. Il est possible de spécifier un nom si on ne veut qu'un runtime global.
- "splitChunks": L'exemple  précédent permet la création d'un bundle séparé contenant toutes les dépendences node

### Pour analyser les bundles

https://webpack.github.io/analyse

https://github.com/webpack-contrib/webpack-bundle-analyzer

https://github.com/samccone/bundle-buddy

https://chrisbateman.github.io/webpack-visualizer

https://survivejs.com/webpack/optimizing/build-analysis

### Sources

https://webpack.js.org/plugins/mini-css-extract-plugin/

https://www.npmjs.com/package/optimize-css-assets-webpack-plugin

https://www.npmjs.com/package/webpack-assets-manifest

https://webpack.js.org/plugins/html-webpack-plugin/

https://github.com/jantimon/html-webpack-harddisk-plugin

  