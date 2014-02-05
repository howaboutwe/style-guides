# HowAboutWe's Sass & View Style Guide

Here are some rules to help keep your Sass and Views maintainable while keeping
your resulting CSS performant in the browser.

## Main Table of Contents
* [Clean up](#clean-up)
* [SASS](#sass)
* [Views](#views), including partials, helpers, and decorators
* [Generating PDF or HTML](#generate-pdf-or-html)

# Clean up

* Generally, you should leave **all** code better off than when you
started, including views and SASS files.
* If you find that view or SASS code violates any the rules in this style guide while completing your story, update the Sass unless:
  * you are behind on sprint or points for that story
  * it is taking longer than 30 mins - 1 hour
* If possible, all clean up commits should be separate from story commits.

# SASS

While there are lessons we've learned in the trenches, start with [this guide from the Sass way](http://thesassway.com/articles/sass-doesnt-create-bad-code-bad-coders-do)

* [Structure](#structure)
* [Classes](#classes)
* [Variables](#variables)
* [Nesting](#nesting)
* [Functions](#functions)
* [Compass](#compass)
* [Extends](#extends)
* [Mixins](#mixins)
* [Placeholders](#placeholders)
* [!important](#important)

## Structure

### Directory Structure

Your `app/assets/stylesheets` directory should have the following structure:

* application
  * base
    * _layout.scss 
    * _navigation.scss 
    * _site_chrome.scss ...
  * lib
    * _colors.scss 
    * _fonts.scss 
    * _mixins.scss ...
  * views
    * profile
    * shared
    * users
    * _search.scss
    * _speed_dates.scss ...

What goes where?
* Fundamental layouts and styles go in `application/base`
* Shared styles (often for shared views) go in `application/views/shared`
* View specific styles go in `application/views`
  * Note: these files will still be compiled into the global
    application.css file and thus should be scoped appropriately

### Code Structure

* Like Ruby code, code should be organized in logical paragraphs
* @includes should be group together at the top of the selector
* @extends should be grouped together after @includes
* Styles for the current selector should be after @extends
* Nested selectors should be last
  * & nested selectors should go above child nested selectors   

For example:

````
.my-class {
  @include adjust-font-size-to($medium-font-size);
  @include border-radius(5px);

  @extend %container;
  @extend %some_other_placeholder;

  color: $cyan;

  &:hover { text-decoration: underline; }

  span { color: $pink; }
}
````

## Classes

* All class names should be hyphenated. 
    * e.g. `my-class`
* Avoid alignment and markup based classes.
  * e.g: `.right { float: right }`
  * e.g: `.patternLight { background: $patternLight }`
  * Use variables or extend placeholders instead
* Use descriptive, semantic class names whenever possible
* Add styles to classes whenever possible, rather than to the element or id
  * Use child or descendant selectors with element selectors **only** if the element selectors can and will be predictable.  
* Avoid qualified selectors as they inhibit reuse and increase browser workload 
  * e.g. `ul.nav {}`
  * e.g. `a.button {}`
* In views
    * Each page should have the class of the controller and action and styles should be scoped accordingly
    * Classes and HTML should describe the content of the page not the style of the page
    
## Variables

* All variables should be camelCase
  * e.g. `$borderRadius`
* Establish a base file for:
  * _sizes.scss with base variables for fonts (font size and line
    height) and grid units (grid, margins, padding)
  * _colors.scss with the colors from your styleguide.
  * **Only** use the established variables for padding, margins, layouts, font sizes, colors, etc.
    * If you need a new color or size, you probably don't.
    * If you **actually** need a new color or size: 
      * Update the base _colors.scss first with a new color variable and update the pattern library, **or**
      * In the case of a one-off color variation, use the `lighten` or `darken` functions on an existing color variable
* Variables should be named based on what they represent, not their value
  * e.g. use `$mediumFontSize` not `$typeSize23` 
* Use variables for repeated values within a file or class declaration
  * e.g. `_nav.scss`

    ````
      $nav_height: 50px;
      nav { height: $nav_height; }
      #main { margin-top: $nav_height*-1; }
    ````
      
## Nesting

[Beware of inception style selectors.](http://thesassway.com/beginner/the-inception-rule)  

* Limit nesting as much as possible as to avoid overly specific and non-modular selectors
* Don't mirror your markup's nesting in your SASS, as tempting as it may be
* Use global styles over page or element specific-styles

## Functions

SASS is code.  Use it as such where appropriate.

* Use math functions for style variations
  * e.g. `$skinny-padding: $base-padding/2` 
* Limit use of control directives (@if, @for etc)

## Compass

[Use it!](http://compass-style.org/) 

* Use compass functions over home-grown solutions 
* Use the
  [vertical rhytym](http://compass-style.org/reference/compass/typography/vertical_rhythm/#mixin-rhythm) function to establish consistency for font sizes, line heights, margins,
and padding.
  * e.g. `@include adjust-font-size-to($medium-font-size)` for changing
    the font size
  * e.g. `@include leader(1)` for adding top space. `margin-leader` and
    `padding-leader` are also available
  * e.g. `@include trailer(1)` for adding bottom space. `margin-trailer`
    and `padding-trailer` are also available


## Extends

* **Do not** extend classes
* **Do** extend [placeholders](http://www.sitepoint.com/sass-mixin-placeholder/)

## Mixins

* Always use a mixin if it exists
* Create a mixin **only** if you intend to pass it **contextual variables**, otherwise you are simply duplicating code and bloating the CSS (in that case, use a placeholder)
* Generally, do not have a class that simply includes a mixin.  
  * Instead, include the mixin in the appropriate content-based class as needed 
  * Bad: `.bevelledSeparator { @include bevelledSeparator; }`
  * Good: `.offer { @include bevelledSeparator; }`

## Placeholders

* Use placeholders when you want a reusable block of code that **does not** require contextual variables
* Placeholders, unlike mixins, take advantage of selector grouping to avoid duplicating code
* To use a placeholder, extend it:

    ````
    %center {
        margin: 0 auto;
        padding: 10px 5px
    }
    
    .main-module {
        @extends %center;
    }
    ````
      
* Read more about the difference between mixins and placeholder [here](http://www.sitepoint.com/sass-mixin-placeholder/)

## !important

Use `!important` proactively, not reactively.
* Only when we know that we want a style to take precedence over all other variations should we use `!important`. 
    * For example, we might know that we **always** want an error message to appear red, regardless of text color of the module it appears in

    ````
      .error { color: #e32!important; }
    ````
      
  * If we find we need to use `!important` to force an override of a style, however, we need to instead go back and reconsider how that styling was written.


# Views

* [General](#general)
* [Partials](#partials)
* [Helpers](#helpers)
* [Decorators](#decorators)

## General

* Logic in views should be extremely limited if at all
  * Prefer the use of partials, helpers, or decorators
* Mark up should be based on content not styles
* Like Ruby code, break code into logical paragraphs for readability
* Break view files into partials where makes sense for readability and
  comprehension 
* Prefer higher level classes on markup in order to move styling
  differences exclusively into CSS

BAD:

````
  :sass
    .cyan { color: $cyan; }        

  .offer
    %span.icon-arrow{ class: @offer.booked? ? 'cyan' : '' }
````

GOOD: 

````
  :sass
    #offer.booking span { color: $cyan; }      

  #offer.booking.user_subscribed.free
    %span.icon-arrow
````

## Partials

* Move reused components into partials
* Prefer containing rendering logic in the partial itself rather than
  outside it
* Paritals should be contained in a div with a class that's the name of the partial
  * ex: ``_payment_items.haml`` should have ``.payment_items`` on line #1
* All modal partials should be in `app/views/shared/modals` directory
* All partials should be tested with RSpec view tests

## Helpers

* Generally prefer decorators over helpers for single class only
  logic
* Helper methods should always be tested
* Limit the tag generation in helpers
  * If the difference is simply html attributes or text, create methods
    specifically for those cases.  
  * See [Decorators](#decorators) for more information on this pattern. 

## Decorators

We use [Draper](https://github.com/drapergem/draper) to implement the
Presenter/Decorator Pattern for our views

* Prefer explicit delegation over `delegate_all`
  * See the [draper
    documentation](https://github.com/drapergem/draper#using-pagination) 
  * delegate_all uses method_missing which encurs performance penalties
* Limit tag generation and rendering in decorator methods to areas where
  the object's state determines rendering
  * If the difference is simply html attributes or text, use a method
    specifically for that.
* All decorator methods should be tested

You should use a decorator when there is complex logic associated with
displaying the object. 
For example:

* to generate a collection of classes that can be used to move much or
  all of the view logic into CSS
* to generate copy changes based on the state of an object
* particulary in the case of complex object state-based logic

#### Complex Logic Examples

Decorators can return object state or method based classes.  Use this as a way to move display differences exclusively into CSS 

  ````
    def price_classes
      classes = []
      classes << 'membersOnly' if members_only
      classes << (free_for_member? ? 'free' : 'paid')
      classes << 'unavailable' if !bookable? || h.offer_booked?(self)
      classes << 'nonMembers' if !members_only && non_member_price > 0
      classes.join(' ')
    end
  ````

  ````
  .offerCard{ class: offer.price_classes }
  ````

  ````
  .pricing{ class: offer.price_classes }
  ````

Decorators can return computed html attributes nicely

  ````
  def booking_form_attributes
    attrs = {
      url: h.offer_booking_path(self, secure: true),
      html: { id: 'new_booking' }
    }

    if h.current_user.has_booked_this_month?
      attrs[:html][:class] = 'track_submit'
      attrs[:html][:"data-event-name"] = "submit: upsell"
    end

    attrs
  end
  ````

  ````
  = form_for(@booking, offer.booking_form_attributes) do |f|
  ````

# Generator PDF or HTML

You can generate a PDF or an HTML copy of this guide using
[Transmuter](https://github.com/TechnoGate/transmuter).

```
   gem install transmuter
   sudo easy_install pygments
   gem install pdfkit
   brew install wkhtmltopdf
```
# 