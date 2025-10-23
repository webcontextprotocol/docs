# Web Context Protocol

A lightweight HTML annotation standard for AI agents to reliably understand and interact with web pages.

[Documentation](https://wcp.dev)

The Web Context Protocol (WCP) is an open standard that enables AI agents and LLM-powered browsers to reliably understand and interact with web pages. Whether you're building an AI browser, enhancing an autonomous agent, or creating AI-powered workflows, WCP provides a standardized way to tell agents exactly what actions do and how they behave.

## Why WCP?

Current AI agents operate blind on the web:
- **40-60% action failure rate** - Agents can't reliably find the "checkout" button among 15 other buttons
- **Unpredictable side effects** - Does clicking navigate away, open a modal, or trigger an async update?
- **Intent confusion** - "Add to Cart" might be a button, a form, or a div with onClick

WCP solves this by letting websites explicitly declare:
- **What an element does** (`action:add`, `action:purchase`, `action:search`)
- **How it behaves** (`effect:navigate`, `effect:modal`, `effect:async`)
- **Where it belongs** (`region:product`, `region:checkout`, `region:search`)

## Quick Start

Add WCP annotations to your HTML:

```html
<!-- E-commerce Product Page -->
<div data-wcp="region:product">
  <button data-wcp="action:add; effect:async; target:#cart; primary:true">
    Add to Cart
  </button>
  
  <button data-wcp="action:purchase; effect:navigate">
    Buy Now
  </button>
</div>

<!-- Search Interface -->
<form data-wcp="action:search; effect:async; target:#results">
  <input type="search" name="q" />
  <button data-wcp="action:search; effect:async; primary:true">
    Search
  </button>
</form>

<!-- Settings Form -->
<form data-wcp="action:submit; effect:async; target:#notification">
  <button type="submit" data-wcp="action:submit; effect:async; primary:true">
    Save Changes
  </button>
  <button type="button" data-wcp="action:navigate; effect:navigate">
    Cancel
  </button>
</form>
```

Now AI agents know exactly:
- ✅ Which button is the primary action
- ✅ Whether clicking will navigate away or update in-place
- ✅ What semantic region they're in
- ✅ Where updates will appear


## Core Vocabulary

### Actions (What it does)
- `navigate` - Links to new page
- `submit` - Submits form/data
- `add` - Adds item to collection
- `remove` - Removes item
- `search` - Performs search
- `filter` - Filters content
- `auth` - Authentication action
- `purchase` - Initiates payment

### Effects (How it behaves)
- `navigate` - Full page load
- `modal` - Opens overlay
- `async` - Updates in-place
- `download` - File download
- `external` - Leaves site

### Regions (Semantic zones)
- `navigation` - Site navigation
- `content` - Main content
- `product` - Product details
- `checkout` - Checkout flow
- `search` - Search interface
- `results` - Search/filter results

### Modifiers
- `primary:true` - Primary action in context
- `target:[selector]` - Where effect applies

## Before & After

### Without WCP
```html
<button class="btn btn-primary" onclick="handleCheckout()">
  Checkout
</button>
```
**AI agent sees:**
- ❌ Generic button
- ❌ Unknown behavior
- ❌ Guessing required

### With WCP
```html
<button class="btn btn-primary" 
        data-wcp="action:purchase; effect:navigate; primary:true"
        onclick="handleCheckout()">
  Checkout
</button>
```
**AI agent sees:**
- ✅ This initiates purchase
- ✅ Will navigate to new page
- ✅ This is the primary action
- ✅ Can predict behavior

## Adoption

Add the registry beacon to track adoption (optional):

```html
<meta name="wcp-registry" content="https://registry.wcp.dev/ping">
```

This sends a daily ping (domain + version only, no user data) to help track protocol adoption.

## License

The Web Context Protocol specification is released under CC0 (public domain). Anyone can implement, extend, or modify it without restriction.

## About

The Model Context Protocol is an open source project run by [Umbrella Mode, Inc.](https://umbrellamode.com) and open to contributions from the entire community.

---

