---
layout: post
title: An ESLint starter kit for React development
category: Development
tags: [javascript, react, eslint]
description: An .eslintrc file to help you get started linting your ReactJS development projects.
comments: true
share: true
---

Linting is essential to writing good JavaScript, and ESLint is, for my money, the best JavaScript linter out there at the moment. It’s super flexible, and also has plugin support which allows for features like ReactJS linting. For those starting a new ReactJS project, or those who want to integrate ESLint into an existing one, here is an `.eslintrc` (configuration) file to get you started.

{% highlight json %}
{
	extends: "eslint:recommended",
	env: {
		"browser": true,
		"node": true,
		"es6": true
	},
	ecmaFeatures: {
		jsx: true
	},
	globals: {
		"$": false
	},
	plugins: ["react"],
	rules: {
		"brace-style": [2, "1tbs"],
		"comma-style": 2,
		"eol-last": 0,
		"indent": [2, "tab", {"SwitchCase": 1}],
		"jsx-quotes": 2,
		"no-console": 0,
		"no-else-return": 2,
		"no-mixed-spaces-and-tabs": [2, "smart-tabs"],
		"no-multi-spaces": 0,
		"no-throw-literal": 2,
		"no-underscore-dangle": 0,
		"react/jsx-boolean-value": 2,
		"react/jsx-curly-spacing": 2,
		"react/jsx-indent-props": [2, "tab"],
		"react/jsx-no-bind": 2,
		"react/jsx-no-duplicate-props": 2,
		"react/jsx-no-undef": 2,
		"react/jsx-uses-react": 2,
		"react/jsx-uses-vars": 2,
		"react/no-danger": 2,
		"react/no-did-mount-set-state": 2,
		"react/no-did-update-set-state": 2,
		"react/no-direct-mutation-state": 2,
		"react/no-multi-comp": 2,
		"react/no-unknown-property": 2,
		"react/react-in-jsx-scope": 2,
		"react/require-extension": [2, { "extensions": [".js", ".jsx"] }],
		"react/self-closing-comp": 2,
		"react/wrap-multilines": 2,
		"strict": [2, "global"]
	}
}
{% endhighlight %}

While I won’t go over every line in this file, there are a couple settings that may not suit everybody’s needs:

- There are no warnings. Everything is treated as an error.
- I use tab indentation, and require that `case` statements be indented one level deeper than their containing `select` statement.
- I use what I consider to be a reasonable subset of the possible React linting rules. For example, I don’t require that properties be in alphabetical order.
- I allow `console` statements.
- Although I really do not like jQuery I’ve set `$` as a global, readonly variable because it’s sometimes needed when working with Bootstrap components.

To actually use this in your project, all you need to do is include both ESLint and the [React ESLint plugin](https://github.com/yannickcr/eslint-plugin-react) in your project. If you’re using Babel, you’ll also want to include [Babel ESLint](https://github.com/babel/babel-eslint) and add the line `parser: "babel-eslint"` to the configuration to enable linting for all of Babel’s features.

This is by no means a be-all-end-all of `.eslintrc` files. Take it as a starting point and use the [extensive ESLint documentation](http://eslint.org/docs/rules/) as well as that of the [React ESLint plugin](https://github.com/yannickcr/eslint-plugin-react#list-of-supported-rules) to customise it to suit your needs.