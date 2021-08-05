---
layout: default
title: Other functions
nav_order: 4
parent: Javascript Client Library
---

# Other Functions
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## `.setToken()`

Sets the token, overwriting whatever token was already saved in the library. This function also resets the token confirmation status.

Pass a string into the function with the new token value.

```javascript
JVerify.setToken('new-jverify-token');
```

## `.getToken()`

Returns the current token as a string.

```javascript
console.log(JVerify.getToken())
```

## `.setVendor()`

Sets the vendor, overwriting whatever was already saved in the library.

Pass a string into the function with the new vendor value.

```javascript
JVerify.setVendor('new vendor');
```

## `.getVendor()`

Returns the current vendor as a string.

```javascript
console.log(JVerify.getVendor());
```

## `.getTokenConfirmed()`

Returns if JVerify's servers have confirmed the current token is valid. This is overwritten whenever there is an authentication issue or the token is changed.

```javascript
if(JVerify.getTokenConfirmed()) {
  // Token is still valid
}
```