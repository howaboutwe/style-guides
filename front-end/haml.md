# HAML Style Guide

## <a name='TOC'>Table of Contents</a>

1. [Basic Conventions](#basic-conventions)
  1. [Whitespace](#whitespace)
  1. [Escapement](#escapement)
  1. [Semantics](#semantics)
1. [Naming Conventions](#naming-conventions)
1. [Rails Helpers](#rails-helpers)
1. [Interpolation](#interpolation)
1. [Forms & AJAX](#forms-ajax)
1. [Content For Blocks](#content-for-blocks)
1. [Scripting Hooks](#scripting-hooks)


## <a name='basic-conventions'>Basic Conventions</a>

  - Use soft tabs (set to 2 spaces).

  - Use double quotes (`class: "hello"`) over single.

  - Soft limit to 80 character line lengths. A little over is _okay_, but avoid anything longer than 100 characters.

  - Inline styles are "never" appropriate, _except_ when applying
    `{ style: "display: none;" }` or `{ style: "visibility: hidden;" }`.

  - Inline JavaScript is _never_ appropriate.

  - Use `key: value` syntax for attributes when possible. Only use
    hashrocket syntax when you must quote a key. Avoid invalid key
    symbols wherever possible:

    ```haml
    -# Good
    %li{ data: { foo: "bar" } }

    -# Okay
    %li{ data: { :"street-address" => "1234" } }

    -# Bad
    %li{ data: { :foo => "bar" } }

    -# Bad
    %li{ :$monies => "Why did I do this?!" }
    ```


### <a name='whitespace'>Whitespace</a>

  - Internally pad your curly braces with an additional space at the
    start and end:

    ```haml
    -# Good
    %li.media-card{ data: { foo: "bar" } }

    -# Good
    %span.category-icon{ class: "icon-#{ item.category.name }" }

    -# Bad
    %li.media-card{data:{foo:"bar"}}

    -# Bad
    %span.category-icon{ class: "icon-#{item.category.name}" }
    ```

  - Place content on a new line after tags:

    ```haml
    -# Good
    %h2
      Welcome to the Site!

    -# Good
    %h2
      = modal.header.title

    -# Bad
    %h2 Welcome to the Site!

    -# Bad
    %h2= modal.header.title
    ```


### <a name'escapement'>Escapement</a>

  - Escape self-closing tags:

    ```haml
    -# Good
    %br/

    -# Good
    %meta{ charset: "utf-8" }/

    -# Bad
    %br
    ```

  - Use entity escapements when appropriate:

    ```haml
    -# Good
    Hello &amp; Goodbye!

    -# Good
    = link_to "Truncated", "#", title: "Long string that trunca&hellip;".html_safe

    -# Bad
    Hello & Goodbye!
    ```


### <a name='semantics'>Semantics</a>

  - Use descriptive element names when possible:

    ```haml
    -# Good
    %ul
      %li
        / Content...

    -# Bad
    .list
      .list-item
        / Content...
    ```

  - Apply `%span`s to inline elements:

    ```haml
    -# Good
    %p
      %span.icon-category
      Foo bar

    -# Bad
    %p
      .icon-category
      Foo bar
    ```

  - Apply `%span`s to inline elements:

    ```haml
    -# Good
    %p
      %span.icon-category
      Foo bar

    -# Bad
    %p
      .icon-category
      Foo bar
    ```

## <a name='naming-conventions'>Naming Conventions</a>

  - IDs and Classes should use _lower_ `dash-case-for-names`.

  - Templates must have a Class or ID at the root level which
    matches the file name. Examples:

    - File named: `_stream_card_date.html.haml`

      ```haml
      -# Good
      .stream-card-date
        %h3
          Stream Card

      -# Good
      %li.stream-card-date
        %h3
          Stream Card

      -# Good
      %li.stream-card-date.media-card
        %h3
          Stream Card

      -# Bad
      %li
        .stream-card-date
          %h3
            Stream Card
      ```

    - File named: `_right_rail.html.haml`

      ```haml
      -# Good
      #right-rail
        %h2
          I'm the right rail!

      -# Bad
      %h2
        I'm the right rail!
      ```


## <a name='rails-helpers'>Rails Helpers</a>

  - Use built-in Rails element helpers whenever possible:

    ```haml
    -# Good
    = link_to user.name, profile_path(user.name)

    -# Good
    = form_tag form_endpoint_path, class: "new_form" do

    -# Good
    = button_tag "Submit", type: "submit"

    -# Bad
    %a{ href: some_url_path }
      Hello!

    -# Bad
    %form{ action: "foo", method: "post" }
    ```

  - Use blocks if you need to wrap an element in another element:

    ```haml
    -# Good
    = link_to path do
      %span.icon-edit
        Edit this
    ```


## <a name='interpolation'>Interpolation</a>

  - Always use double quotes, even if you don't need to interpolate. Helps
    make code changes a bit cleaner later.

  - Use curly braces when you need multiple tags within a single line:

    ```haml
    -# Good
    Contact us via #{ link_to "Email", email.address } if you need further support.

    -# Bad
    Contact us via
    = link_to "Email", email.address
    if you need further support.
    ```


## <a name='forms-ajax'>Forms & AJAX</a>

  - Always use Rails' [Form Tag](http://api.rubyonrails.org/classes/ActionView/Helpers/FormTagHelper.html#method-i-form_tag)
    or [Form For](http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html#method-i-form_for)
    helpers. Set actions to API endpoints, models, or records whenever possible.
    Use actual routes helpers instead of plain strings.

    ```haml
    -# Good
    = form_for current_user do

    -# Good
    = form_tag api_endpoint_path, remote: true do

    -# Bad
    = form_for "/api-endpoint", remote: true do

    -# Bad
    %form{ anything: "you already failed" }
    ```

  - Always use Rails' built in `remote: true` option for links, forms, or buttons
    that trigger AJAX requests.

    ```haml
    -# Good
    = link_to "Favorite", api_favorites_path, remote: true do

    -# Good
    = button_tag "Open Modal", data: { resource: api_endpoint_path }, remote: true

    -# Bad
    = button_tag "Open Modal", data: { resource: api_endpoint_path }
    ```


## <a name='content-for-blocks'>Content For Blocks</a>

  - Place `- content_for` blocks at the head of a file. The only exception is if
    the block is a child of a parent block, which must be further down a page.

  - Always put `:javascript`, `:coffeescript`, or `:css` blocks in an appropriate
    `content_for` block. Eg- `- content_for :head` for CSS or `- content_for :footer`
    for scripts.


## <a name='scripting-hooks'>Scripting Hooks</a>

  - Never apply JavaScript to a style-oriented classname. Create a new
    classname that is free of styles for binding functionality.

  - Feel free to use `[data-*]` attributes as hooks.

