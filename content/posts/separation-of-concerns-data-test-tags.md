---
title: "Separation of concerns in HTML: data-test- and data-js- attributes"
date: 2020-05-24T23:01:27+03:00
draft: false
---

HTML is used by all websites to contain content, tie it with css. It is mostly
made interactive by some form of javascript and tested by feature tests.

CSS, javascript and tests can "access" elements via selectors in the given ways (or in combination of them):

1. Via id: `#first-section`
2. Via class name: `.post-section`
3. Via data-attributes: `[data-attribute="value"]`
4. Via tag: `section`

In most cases, css nowadays has settled on class names, a lot of feature tests use ids or special class names.

This practice makes HTML risky and hard to change as whenever css is changed, all corresponding
javascript and tests must change.

Refactoring can become especially tricky, especially when class names are tied to all 3.

This mess can be avoided by decoupling:
- To find an element in javascript, add an attribute like `data-js-element` to the HTML
- To find an element in feature tests, add an attribute like `data-test-element` to the HTML.
- Class names are only used for tying CSS

## Example: Interactive tooltips

eAgronom uses [tippy.js](https://atomiks.github.io/tippyjs/) extensively to show tooltips.

Here's how the integration is set up in javascript:

#### HTML

{{< highlight HTML "linenos=table" >}}
<i
  class="material-icons"
  data-js-tippy="Product not in stock"
  data-test-not-in-stock-warning
>
  warning
</i>
{{< / highlight >}}

#### Javascript

{{< highlight javascript "linenos=table" >}}
import $ from 'jquery'
import tippy from 'tippy.js'

tippy('[data-js-tippy]')
{{< / highlight >}}

#### Feature test (Capybara)

{{< highlight ruby "linenos=table" >}}
require 'rails_helper'

RSpec.feature 'Inventory' do
  scenario 'product not in stock warning shown' do
    # Setup, navigation

    expect(page).to have_selector('[data-test-not-in-stock-warning]')
  end
end
{{< / highlight >}}
