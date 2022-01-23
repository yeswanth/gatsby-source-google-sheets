# gatsby-source-google-sheets

This is a fork of the now unmaintained [brandonmp/gatsby-source-google-sheets](https://github.com/brandonmp/gatsby-source-google-sheets/). We've [tried to get in touch](https://github.com/brandonmp/gatsby-source-google-sheets/issues/46) with the original author without success. Thanks @brandonmp for a great plugin.

This fork fixes some problems with the original and unmaintained version, especially support for Sheets v4 API since the [v3 is deprecated](https://cloud.google.com/blog/products/g-suite/migrate-your-apps-use-latest-sheets-api). It also supports newer versions of Gatsby.

We will actively maintained this plugin since we use it ourselves 🙂

## Why?

Why go through the hassle of setting up a complicated headless CMS when Google Sheets already has user permissions, revision history, and a powerful UI? 

This source plugin for [Gatsby JS](https://github.com/gatsbyjs/gatsby) will turn any Google Sheets worksheet into a GraphQL type for build-time consumption. 

## How to:

### Step 1: set up sheets/permissions

Note that if you are signed in to several accounts on Google, you need to make sure that you are using the correct account during the whole procedure below,
especially if you open a new browser tab.

1. If you haven't already, start by creating a [Google Cloud Project](https://console.cloud.google.com/).
2. Enable [Google Sheets API](https://console.cloud.google.com/apis/api/sheets.googleapis.com). Make sure to do it for the **correct project**. 🙂
3. Create a [Google Service Account](https://developers.google.com/identity/protocols/OAuth2ServiceAccount#creatinganaccount). Note that this account doesn't need
any specific permission or roles since you will share the sheets explicitly.
4. Download the credentials file (see how to use it below).
5. Open your google sheet, click "File > Share..." and enter your service account's e-mail address (you can find it in the credentials file).

### Step 2: configure your gatsby project

Standard source plugin installation.

```
yarn add https://github.com/yeswanth/gatsby-source-google-sheets#[version-number]


// gatsby-config.js
// ...
{
    resolve: 'gatsby-source-google-sheets',
    options: {
        spreadsheetId: 'get this from the sheet url',
        worksheetTitle: 'ie the name in the worksheet tab',
        credentials: require('./path-to-credentials-file.json')
    }
},
// ...

```

The plugin makes the following conversions before feeding Gatsby nodes:
1. Numbers are converted to numbers. Sheets formats numbers as comma-delineated strings, so to determine if something is a number, the plugin tests to see if the string (a) is non-empty and (b) is composed only of commas, decimals, and digits:
```
if (
    "value".replace(/[,\.\d]/g, "").length === 0 
      && "value" !== ""
   ) { 
    ...assume value is a number and handle accordingly
}
```
2. "TRUE"/"FALSE" converted to boolean true/false
3. empty cells ("" in sheets payload) converted to null
4. Google sheets mangles column names and converts them all to lower case
5. Dates are recognized as dates


## Troubleshooting
1. If you get the error "No key or keyFile set", make sure you are using a Service Account API key and not a simple API key.
2. If you get the error "Cannot read property 'worksheets' of undefined", make sure you have shared your spreadsheet with your service account user.
