---
title: Processing Payments with Square
---

Square is a payment service that emphasizes quick, secure payments as well as a user-friendly and affordable point of sale (POS) system. You may have already seen their tiny credit card readers, which are great for mobile businesses like those that sell via food trucks or craft fairs. This guide explains how to begin using Square with your Gatsby site.

## Prerequisites

You'll need to [set up a developer account](https://squareup.com/signup?v=developers) to get started. Create a new application from your developer dashboard. The name of your application cannot include the word "square". Once that's done, you should see your new application on your dashboard. Each application has an application ID and access token associated with it.

## Choosing the right product

Square can handle most of your payment-related needs including accepting payments, managing product catalogs, and managing payroll. Begin by determining which of these products you want to incorporate into your business. This guide focuses on [accepting payments on your website](https://developer.squareup.com/docs/online-payment-options#square-payments-in-your-own-website) but you may wish to use their in-person or in-app options as well.

You've got two options for integrating Square payments: redirecting to a hosted checkout page with the Checkout API _or_ taking payments from your Gatsby site using the `SqPaymentForm` library with the Payments API.

### Redirecting to a hosted checkout page

Redirecting to a Square-hosted page takes some of the pressure off since you don't need to build a checkout page. However, getting that functionality "for free" does come with some restrictions. You will not be able to customize the UI of the page users are sent to once they're ready to checkout. Square only recommends this option for situations where accepting payments on your own site isn't feasible.

### Accepting Square payments

Square recommends using the Payments API instead because it offers much greater flexibility. You can customize not only the look and feel of the checkout process but also the checkout process itself, such as customizing the payment methods available.

This process is broken into two steps:

1. Generate a single-use token called a nonce.
2. Charge whatever payment source the user has provided (this could be a credit card, gift card, etc.) using the nonce.

To add a Square payment form to your Gatsby site, you'll need to load the JavaScript that runs Square's payment form into the `<head>` of your website. You can do this by calling `setHeadComponents()` in `gatsby-ssr.js`:

```js:title=gatsby-ssr.js
import React from "react";

export const onRenderBody = ({ setHeadComponents }) => {
  setHeadComponents([
    <script src='https://js.squareup.com/v2/paymentform'></script>
  ]);
};

```

You'll also need to create some variation of a `PaymentForm` component. Square maintains a few [payment form templates](https://github.com/square/connect-api-examples/tree/master/templates/web-ui/payment-form) you can base your component on. Try starting with the ["basic" JavaScript file](https://github.com/square/connect-api-examples/blob/master/templates/web-ui/payment-form/basic/sqpaymentform-basic.js). They also provide a [running example](https://codesandbox.io/s/4zjrv7kry9?from-embed) using their "basic-digital-wallet" template with React.

Once that's done, you can use the `SqPaymentForm` object available on the `window` (you get this from the Sqaure JS you called in the `<head>`) and pass it in via props whenever you want the form to show up! In the example below, the `PaymentForm` has been added to the `Layout` component from the [default starter](/starters/gatsbyjs/gatsby-starter-default/).

```js:title=layout.js
import React from "react"
import PropTypes from "prop-types"
import { useStaticQuery, graphql } from "gatsby"

import Header from "./header"
import "./layout.css"

import PaymentForm from './paymentForm';

const Layout = ({ children }) => {
  const data = useStaticQuery(graphql`
    query SiteTitleQuery {
      site {
        siteMetadata {
          title
        }
      }
    }
  `)

  return (
    <>
      <Header siteTitle={data.site.siteMetadata.title} />
      <div
        style={{
          margin: `0 auto`,
          maxWidth: 960,
          padding: `0 1.0875rem 1.45rem`,
        }}
      >
        <main>{children}</main>
        <PaymentForm paymentForm={ window.SqPaymentForm } />
        <footer>
          © {new Date().getFullYear()}, Built with
          {` `}
          <a href="https://www.gatsbyjs.org">Gatsby</a>
        </footer>
      </div>
    </>
  )
}

Layout.propTypes = {
  children: PropTypes.node.isRequired,
}

export default Layout
```

## The Square sandbox

You can test your setup [using the Square sandbox](https://developer.squareup.com/docs/testing/sandbox). To do so, you'll need to return to your [developer dashboard](https://developer.squareup.com/apps). In the "Credentials" tab, you can toggle back and forth between your production and sandbox credentials!

## Other resources

- [`SqPaymentForm` documentation](https://developer.squareup.com/docs/api/paymentform#navsection-paymentform)
- [Square's tutorial for online payment options](https://developer.squareup.com/docs/online-payment-options)
- Square's blog post on [Online Payments with React + Square](https://developer.squareup.com/blog/online-payments-form-react/)
- [`gatsby-plugin-square-payment-form`](/packages/gatsby-plugin-square-payment-form/)