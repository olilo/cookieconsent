# Cookieconsent

This is a modified version of the [cookieconsent script](https://github.com/brainsum/cookieconsent/) by BRAINSUM

changes:
- does not use inline styles, offers style compilation (pre-processor: SCSS or less) instead
- adds a table with cookie specific information for each cookie category
- removes cookies after revoked consent

##why
Cookieconsent adds a cookie consent message as required by european data privacy law. It takes into account:
- EU regulation General Data Protection Regulation (EU) 2016/679 ([**GDPR**](https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=CELEX:32016R0679&from=EN)) 
- judgment ([**preliminary ruling for C-673/17**](http://curia.europa.eu/juris/document/document.jsf;jsessionid=D5DC4CA415C605B28E70747FD3C5158C?text=&docid=218462&pageIndex=0&doclang=EN&mode=req&dir=&occ=first&part=1&cid=1458627)) 
of the European Court of Justice (ECJ)
- the current [draft of the future ePrivacy Regulation (**ePR**)](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=celex:52017PC0010) - Proposal for a REGULATION OF THE EUROPEAN PARLIAMENT AND OF THE COUNCIL concerning the respect for private life and the protection of personal data in electronic communications and repealing Directive 2002/58/EC (Regulation on Privacy and Electronic Communications). 

##what
- displays a basic consent message (consent bar) till user consents to a certain amount of cookies and 
offers additional information and selectable option in an modal (consent modal)
- user can consent to all cookies or only certain categories of them (Opt-In), 
- consent bar is hidden after the consent is given and the setting is stored in a cookie
- user can revoke the consent anytime by reopening the consent modal and editing it (Opt-Out), cookies for which there is no consent are deleted

### Data privacy features
- **Opt-In**: blocks cookies that are not strictly necessary for the use of the website till consent is given
- **Opt-Out**: consent is revocable
- **cookie information requirements**: displays information about the cookie categories and lists all cookies with name, purpose, storage duration and type

### Features
- multilingual
- responsive

### Blocking methods 
- **dynamic script tags**: intercept script tags that are inserted dynamically at page load
- **static script tags**: inactivate script tags in the markup
- **script wrapping**: create a global wrapper function to wrap and block JS code      
- **local domain cookies**: override the browser's COOKIE SET method       



### What's included
```text
dist/
└── cookiecontent.css
└── cookiecontent.min.css
└── cookiecontent.js
└── cookiecontent.map
└── cookiecontent.min.js
src/
└── less/
     └── bar.less
     └── config.less
     └── modal.less
└── scss/
     └── bar.scss
     └── config.scss
     └── modal.scss
```
the dist directory provides compiled CSS and JS (cookiecontent.*), as well as compiled and minified CSS and JS (cookiecontent.min.*). A source map (cookiecontent.map) is available for use with certain browsers' developer tools. 

If you want to integrate the pre-processor files in you own tool chain, you can use the uncompiled style modules in src/less or src/scss to do so. 
### Usage
1. Include the script file into your HTML head before **anything** else.
    ```
    <script src="cookieconsent.min.js"></script>
    ```
2. Add the style sheet (or integrate the pre-processor styles).
    ```
    <link rel="stylesheet" href="cookieconsent.min.scss" />
    ```
3. configure the configuration object with the cookie information of your project.
4. Call the init() function with the configuration object.

### Configuration
CookieConsent does not detect the information automatically, everything must be configured manually for the website:
- categories
- blocking method
- cookie information etc.

#### Configuration object

The script is being controlled mainly by a configuration object which is passed to the inital call. It acts as configuration and a global state object.

```javascript
<script>
  window.CookieConsent.init({
    // More link URL in the head section of the modal, can be null or a string
    modalMainTextMoreLink: null, 
    
    // Time before the bar appears in ms
    barTimeout: 1000,
    
    // Look and feel
    language: {
        
      // Current language code, which is used in the 
      current: 'en',
      
      // Translations
      locale: {
          
        en: {
            
          // texts for the bar
          barMainText: 'This website uses cookies to make it more user-friendly, to analyse and statistically evaluate the use of our website and to save your data protection settings. By selecting "Accept", you agree that we may use cookies and related technologies as described in our Privacy Policy and Cookie Notices. For a detailed selection and description of all cookies, please select "Manage Cookie Settings".',
          barLinkSetting: 'Manage Cookie Settings',
          barBtnAcceptAll: 'Accept all cookies',
          
          // texts for the modal
          modalMainTitle: 'Manage Cookie Settings',
          modalMainText: 'Cookies are small piece of data sent from a website and stored on the user\'s computer by the user\'s web browser while the user is browsing. Your browser stores each message in a small file, called cookie. When you request another page from the server, your browser sends the cookie back to the server. Cookies were designed to be a reliable mechanism for websites to remember information or to record the user\'s browsing activity.',
          modalBtnSave: 'Save current settings',
          modalBtnAcceptAll: 'Accept all cookies',
          modalAffectedSolutions: 'Affected solutions:',
          learnMore: 'Learn More',
          on: 'On',
          off: 'Off',
        }
      }
    },
    
    // List all the categories you want to display
    categories: {
        
      // Has to be a unique name
      // This probably will be the default category for cookies
      necessary: {
          
        // The cookies here are strictly necessary for the use of the website 
        // can't be turned off.
        // Wanted config value will be ignored.
        needed: true,
        
        // The cookies in this category will be let through.
        wanted: true,
        
        // If the checkbox is on or off at first run.
        checked: true,
        
        // Translations for category
        language: {
          locale: {
            en: {
              name: 'Strictly Necessary Cookies',
              description: 'Necessary cookies help to make a website usable by enabling basic functions such as page navigation and access to secure areas of the website. The website cannot function properly without these cookies.'
            }
          }
        }
      },
      // The cookies here are for Google Analytics and not strictly necessary for the use of the website 
      // They have to be turned off initially.
      statistics: {
          
        // Not strictly necessary  
        needed: false,
        
        // Turned off initially
        wanted: false,
        checked: false,
        
        // cookie that are set in this category
        cookies: ['__utma', '__utmb','__utmc','__utmt','__utmz'],
        
        // Translations for category
        language: {
            locale: {
              en: {
                name: 'Statistics Cookies',
                description: 'Statistics cookies help us to understand how visitors interact with our website. All information is collected and reported anonymously.',
              }
            }
        }
      }
    },
    
    // List actual services here
    services: {
        
      // Unique name
      needed: {
          
        // Existing category Unique name
        category: 'necessary',
        // strictly necessary are not blocked, so does  not matter, which type you use here
        type: 'localcookie',
        
        // translations for the cookie information in the modal
        language: {
          locale: {
             en: {
               name: 'CMS and privacy settings',
               infos: [
                 // This cookie is set by cookieconsent  
                 {cname: 'cconsent', ctype: 'HTTP - persistent cookie', cdomain: '.example.de', cduration: '2 years',cdesc: 'storage of cookie settings'},
                 {cname: 'csrftoken', ctype: 'HTTP - persistent cookie', cdomain: 'www.example.de', cduration: '1 day',cdesc: 'CSRF protection for the website'},
               ]
             }
          }
        }
      },
      
      // Unique name
      ganalytics: {
          
        // Existing category Unique name
        // This example shows how to block Google Analytics
        category: 'statistics',
        
        // Type of blocking to apply here.
        // This depends on the type of script we are trying to block
        // Can be: dynamic-script, script-tag, wrapped, localcookie
        type: 'dynamic-script',
        
        // Only needed if "type: dynamic-script"
        // The filter will look for this keyword in inserted script tags
        // and block if match found
        search: 'analytics',
        
        // translations for the cookie information in the modal
        language: {
          locale: {
            en: {
              name: 'Google Analytics',
              infos: [
                {cname: '__utm.gif', ctype: 'Pixel',cduration: 'Session',cdesc: 'Used to determine which device and browser is used by the user'},
                {cname: '__utma', ctype: 'HTTP - persistent cookie',cduration: '2 years',cdesc: 'collects data on how many times a user visited the site, as well as the first and last visit'},
                {cname: '__utmb', ctype: 'HTTP - persistent cookie',cduration: '1 day',cdesc: 'registers a timestamp of the webpage access time to calculate the duration of the visit'},
                {cname: '__utmc', ctype: 'HTTP - Session cookie',cduration: 'Session',cdesc: 'register a timestamp with the time of leaving the website to calculate the duration of the visit'},
                {cname: '__utmt', ctype: 'HTTP - persistent cookie',cduration: '1 day',cdesc: 'Used to throttle the speed of requests to the server'},
                {cname: '__utmz', ctype: 'HTTP - persistent cookie',cduration: '6 months',cdesc: 'Collects data from where the user came from (e.g. which search engine was used or which link was followed)'}
              ]
            }
          }
        }
      }
    }
  });
  </script>
```
### Development
#### Requirements
* less / SASS
* npm
* node.js

#### Setup
* Clone this project
* Initialize npm for project
    ```sh
    npm install
    ```
#### 
* Start up a development server, which will automatically rebuild cookieconsent as you change files
     ```sh
     # includes SASS compiling
     npm run dev 
     # includes less compiling
     npm run dev:less 
     ```
     server is running at http://localhost:1234 
     
* Builds the assets 
     ```sh
     # build JS and SASS minified
     npm run build
     # OR 
     # build JS and less minified
     npm run build:less
     # OR 
     # build JS and SASS
     npm run build:dev
     # OR 
     # build JS and less
     npm run build:dev:less
     ```