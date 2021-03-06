# ES6 構文の例

JavaScriptの使用経験が（比較的）少ない方や、あまり経験がない方は、ES6 がどのようなもので、どのような便利な機能が含まれているのかをご存知ないかもしれません。 このガイドは主にDiscordボットのためのものなので、ES6 の恩恵を受けるために使用できるものと、実行できる例としてdiscord.jsを使用したコードを使います。

今回使用するコードは以下の通りです。

```js
const Discord = require('discord.js');
const client = new Discord.Client();
const config = require('./config.json');

client.once('ready', () => {
    console.log('Ready!');
});

const { prefix } = config;

client.on('message', message => {
    if (message.content === prefix + 'ping') {
        message.channel.send('Pong.');
    } else if (message.content === prefix + 'beep') {
        message.channel.send('Boop.');
    } else if (message.content === prefix + 'server') {
        message.channel.send('ギルド名: ' + message.guild.name + '\nメンバーの総数: ' + message.guild.memberCount);
    } else if (message.content === prefix + 'user-info') {
        message.channel.send('あなたのユーザー名: ' + message.author.username + '\nあなたのID: ' + message.author.id);
    }
});

client.login(config.token);
```

お気づきかもしれませんが、このコードはすでに ES6 を少し使用しています。 `const` と アロー関数宣言 (`() => ...`) は ES6 の構文であり、可能な限り使用することをオススメします。

上記のコードは改善できる箇所がいくつかあります。 それを見ていきましょう。

## テンプレート リテラル

上記のコードを確認してみると `prefix + 'name'` や `'Your username: ' + message.author.username` のようなことを行っていますが、これは正しいです。 ただ少し読みづらいし、これを打ち込むのはあんまり楽しくないでしょう。 ですが、幸いなことに良い代替手段があります。

```js
// 現在のES5バージョン
else if (message.content === prefix + 'server') {
    message.channel.send('ギルド名: ' + message.guild.name + '\nメンバーの総数: ' + message.guild.memberCount);
}
else if (message.content === prefix + 'user-info') {
    message.channel.send('あなたのユーザー名: ' + message.author.username + '\nあなたのID: ' + message.author.id);
}
```

```js
// ES6 version, using template literals
else if (message.content.startsWith(`${prefix}server`)) {
    message.channel.send(`ギルド名: ${message.guild.name}\nメンバーの総数: ${message.guild.memberCount}`);
}
else if (message.content.startsWith(`${prefix}user-info`)) {
    message.channel.send(`あなたのユーザー名: ${message.author.username}\nあなたのID: ${message.author.id}`);
}
```

読みやすいそして書くのも簡単！ 両方のいいところを兼ね備えてるね。

### テンプレートリテラル vs 文字列の連結

他のプログラミング言語を使ったことがある方は、「文字列補間」という言葉を知っているかもしれません。 テンプレートリテラルは、JavaScriptの文字列補間の実装になります。 heredoc の構文をご存知の方は、それによく似ています。文字列の補間や、複数行の文字列も可能です。

下の例ではあまり詳しく説明しませんが、興味のある方は[MDNを読んでみてください。](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Template_literals)

```js
// variables/function used throughout the examples
const username = 'Sanctuary';
const password = 'pleasedonthackme';

function letsPretendThisDoesSomething() {
    return 'Yay for sample data.';
}
```

```js
// 通常の文字列連結
console.log('Your username is: **' + username + '**.');
console.log('Your password is: **' + password + '**.');

console.log('1 + 1 = ' + (1 + 1));

console.log('And here\'s a function call: ' + letsPretendThisDoesSomething());

console.log(
    'Putting strings on new lines\n'
    + 'can be a bit painful\n'
    + 'with string concatenation. :(',
);
```

```js
// テンプレートリテラル
console.log(`Your password is: **${password}**.`);
console.log(`Your username is: **${username}**.`);

console.log(`1 + 1 = ${1 + 1}`);

console.log(`And here's a function call: ${letsPretendThisDoesSomething()}`);

console.log(`
    Putting strings on new lines
    is a breeze
    with template literals! :)
`);

// NOTE: テンプレートリテラルは、中のインデントもレンダリングします。
// それを回避する方法がありますので、別のセクションでご紹介します。
```

いかに簡単で読みやすいものになっているかがわかりますね。 場合によっては、コードが短くなることもあります。 これは、できるだけ活用したいものです。

## アロー関数

Arrow functions are shorthand for regular functions, with the addition that they use a lexical `this` context inside of their own. If you don't know what the `this` keyword is referring to, don't worry about it; you'll learn more about it as you advance.

Here are some examples of ways you can benefit from arrow functions over regular functions:

```js
// regular functions, full ES5
client.once('ready', function() {
    console.log('Ready!');
});

client.on('typingStart', function(channel, user) {
    console.log(user + ' started typing in ' + channel);
});

client.on('message', function(message) {
    console.log(message.author + ' sent: ' + message.content);
});

var doubleAge = function(age) {
    return 'Your age doubled is: ' + (age * 2);
};

// inside a message collector command
var filter = function(m) {
    return m.content === 'I agree' && !m.author.bot;
};

var collector = message.createMessageCollector(filter, { time: 15000 });
```

```js
// arrow functions, full ES6
client.once('ready', () => console.log('Ready!'));

client.on('typingStart', (channel, user) => console.log(`${user} started typing in ${channel}`));

client.on('message', message => console.log(`${message.author} sent: ${message.content}`));

const doubleAge = age => `Your age doubled is: ${age * 2}`;

// inside a message collector command
const filter = m => m.content === 'I agree' && !m.author.bot;
const collector = message.createMessageCollector(filter, { time: 15000 });
```

There are a few important things you should note here:

* The parentheses around function parameters are optional when you have only one parameter but are required otherwise. If you feel like this will confuse you, it may be a good idea to use parentheses.
* You can cleanly put what you need on a single line without curly braces.
* Omitting curly braces will make arrow functions use **implicit return**, but only if you have a single-line expression. The `doubleAge` and `filter` variables are a good example of this.
* Unlike the `function someFunc() { ... }` declaration, arrow functions cannot be used to create functions with such syntax. You can create a variable and give it an anonymous arrow function as the value, though (as seen with the `doubleAge` and `filter` variables).

We won't be covering the lexical `this` scope with arrow functions in here, but you can Google around if you're still curious. Again, if you aren't sure what `this` is or when you need it, reading about lexical `this` first may only confuse you.

## 分割代入

Destructuring is an easy way to extract items from an object or array. If you've never seen the syntax for it before, it can be a bit confusing, but it's straightforward to understand once explained!

### オブジェクトの分割代入

Here's a common example where object destructuring would come in handy:

```js
const config = require('./config.json');
const prefix = config.prefix;
const token = config.token;

// Alternative version (not recommended)
const prefix = require('./config.json').prefix;
const token = require('./config.json').token;
```

This code is a bit verbose and not the most fun to write out each time. Object destructuring simplifies this, making it easier to both read and write. Take a look:

```js
const { prefix, token } = require('./config.json');
```

Object destructuring takes those properties from the object and stores them in variables. If the property doesn't exist, it'll still create a variable but with the value of `undefined`. So instead of using `config.token` in your `client.login()` method, you'd simply use `token`. And since destructuring creates a variable for each item, you don't even need that `const prefix = config.prefix` line. Pretty cool!

Additionally, you could do this for your commands.

```js
client.on('message', message => {
    const { content } = message;

    if (content === `${prefix}ping`) {
        // ping command here...
    } else if (content === `${prefix}beep`) {
        // beep command here...
    }
    // other commands here...
});
```

The code is shorter and looks cleaner, but it shouldn't be necessary if you follow along with the [command handler](/command-handling/) part of the guide.

You can also rename variables when destructuring, if necessary. A good example is when you're extracting a property with a name already being used or conflicts with a reserved keyword. The syntax is as follows:

```js
// `default` is a reserved keyword
const { 'default': defaultValue } = someObject;

console.log(defaultValue);
// 'Some default value here'
```

### 配列の分割代入

Array destructuring syntax is very similar to object destructuring, except that you use brackets instead of curly braces. In addition, since you're using it on an array, you destructure the items in the same order the array is. Without array destructuring, this is how you'd extract items from an array:

```js
// assuming we're in a `profile` command and have an `args` variable
const name = args[0];
const age = args[1];
const location = args[2];
```

Like the first example with object destructuring, this is a bit verbose and not fun to write out. Array destructuring eases this pain.

```js
const [name, age, location] = args;
```

A single line of code that makes things much cleaner! In some cases, you may not even need all the array's items (e.g., when using `string.match(regex)`). Array destructuring still allows you to operate in the same sense.

```js
const [, username, id] = message.content.match(someRegex);
```

In this snippet, we use a comma without providing a name for the item in the array we don't need. You can also give it a placeholder name if you prefer, of course; it's entirely preference at that point.

## var, let そして const

Since there are many, many articles out there that can explain this part more in-depth, we'll only be giving you a TL;DR and an article link if you choose to read more about it.

1. The `var` keyword is what was (and can still be) used in JavaScript before `let` and `const` came to surface. There are many issues with `var`, though, such as it being function-scoped, hoisting related issues, and allowing redeclaration.
2. The `let` keyword is essentially the new `var`; it addresses many of the issues `var` has, but its most significant factor would be that it's block-scoped and disallows redeclaration (*not* reassignment).
3. The `const` keyword is for giving variables a constant value that is unable to be reassigned. `const`, like `let`, is also block-scoped.

The general rule of thumb recommended by this guide is to use `const` wherever possible, `let` otherwise, and avoid using `var`. Here's a [helpful article](https://madhatted.com/2016/1/25/let-it-be) if you want to read more about this subject.
