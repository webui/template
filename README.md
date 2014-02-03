WebUI Template
========

WebUI Template - hierarchical template engine. Build on JavaScript.

Can be used to render templates on the fly (on the client-side), on the server (with [node.js](http://nodejs.org)) or precompile to JavaScript code with [Grunt.js](http://gruntjs.com) to run as pure JavaScript code.

Part of [WebUI](https://github.com/webui) library.


The idea
-------

We selected to create elegant and powerful templates, more similar to [Jade](http://jade-lang.com) and [HAML](http://haml.info), instead of widely used approach like [Django Templates](https://docs.djangoproject.com/en/dev/topics/templates/), [Smarty](http://www.smarty.net) or [Handlebars.js](http://handlebarsjs.com).


What problems do we solve?
-------

 - inheritance like Django Templates, but with avoid problem with multiple blocks with the same name in the same file
 - each variable inside template (`$var_name`) is wrapped to container with helpers. It allows us to handle situations when we try to convert `null` value to upper case to avoid error on this step
 - **All tags closed correctly**. Not closed tags gone! All pair tags will be closed correctly!
 - **Server-side and client-side support**. Enable RIA web apps - render templates on both server-side and client-side from the same templates! It's the best solution if you use node.js.
 - We plan to create template rendering for: Python, Ruby, PHP and client-side JavaScript


Example of use
-------

### compile template to html

```javascript
var html = $webui.template.render('news', {
  page: {
    title: 'Hello World',
    keywords: 'webui template, node.js, javascript, template engine'
  },
  user: {
    name: 'Anton',
    email: 'anton@...com'
  },
  news: [
    {title: 'First article', content: 'This is first article'},
    {title: 'Second article', content: 'This is second article'}
  ],
});

console.log(html);
```

### Templates

```yaml
# base.html
html
  head
    title: $page.title
    meta name="keywords" content="$page.keywords"
    link href="http://netdna.bootstrapcdn.com/bootstrap/3.0.3/css/bootstrap.min.css" rel="stylesheet" media="screen"
    script src="http://netdna.bootstrapcdn.com/bootstrap/3.0.3/js/bootstrap.min.js"

  body
    div.top_menu
      a href="/": Home
      a href="/news": News
      a href="/login": Log in
    
    h1: $page.title

    div.header: $page.header
    
    div.content: $page.content
    
    div.footer
      p.links
        a href="/about": About
        a href="/stats": Statistics
```

```yaml
# news.html

[extend: base.html]

$page.title: $page.title. Top News

$page.header
  p: News
  p: Add new article

$page_content
  div.user_info
    [if $user.is_authenticated]
      p: $user.name
      p: $user.email
    [else]
      a href="/login": Login
  
  div.articles_list
    [for $article in $news]
      div.article
        a href="[url show_article id=$article.id]": $article.title
        div: [load votes.html]
        div: $article.content.escape(',."').nl2br
```

```yaml
# votes.html
$actual_vote: $actual_vote.default(0)

div.votes
  [for $rate in 1..5]
    $is_active: $actual_vote.is_equal($rate, 'active', 'inactive')
    a.star
      img class="$is_active"
```


Syntax
--------

Still under development.

 * `$name` - get or set variable. We can access properties of object, like `$user.name`
 * `1..5` - set range from 1 to 5. Like in Ruby
 * `[extend: base.html]` - extend other template. In other words, push local defined variables to given template
 * `[if ...] ... [elif ...] ... [else] ...` - condition for inner block
 * `[for $obj in $objects] ... [else] ...` - loop of inner block
 * `[url show_article id=$article.id]` - call `url` function and pass `'show_article'` as first argument and then named argument `id = $article.id`
 * `$article.content.escape(',."').nl2br` - we can use pipes looks like in unix shell. And we can use parameters to functions. It's key-chain syntax like this `$article.date_create.date_format('DD/MM/YYYY' time_zone='+2 GMT').escape`
 * `$actual_vote.is_equal($rate, 'active', 'inactive')` - inline `if` condition
