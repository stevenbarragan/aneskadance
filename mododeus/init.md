**Project**

Anesca Dance

**Objective**

Build a frontend app that displays and manages a list of studios and dance clases from stripe.

**Features**
* **Serverless** Hono framework
* **Infrastructure** Cloudflare Pages Mode
* **Authentication** Use Better Auth with Google OAuth | No database setup
* **Multiple Studios** Two studios, two different Stripe accounts, US and Brazil
* **Image** Use Cloudflare images
* **Booking** Use Stripe
  * Use a different Stripe account for each studio
  * Classes are products in Stripe with payment links

**Requirements**
* **Backend** called `Modo Deus`
  * Lives in /mododeus | Make sure is not indexed by search engines
  * Need to be authenticated to be used
  * Only people with people with emails specified in an environemnt variable can access it
  * Setup CORS appropetly
    * Origin should come from the environment variable | defaults to localhost
    * Port comes from environment variable | defaults to 8080
  **Workflow**
    * Authenticate user using Better Auth (better-auth package) and Google OAuth
    * Set session current studio to US if not set
    * Display studio selector to set sesssion current studio
      * Set session to selected when changed
    * Displays list of classes for current studio from stripe
      * List of all active classes | Payment links from Stripe
      * Clicking on a class will display the class details
      * Button to list people who have booked the class
        * Get customers list from Stripe's payment link
        * Display list of people who booked the clase
      * Do not display product image
      * Option to add a new class
    * Display list of current coupons from Stripe and its codes
      * Name
      * Horizontal list of codes and its expiration date
      * Horizontal list of applicable product names for this coupon from Stripe
        * Expand the applies_to property to get the product details and get applicable products ids.
        * Get product names using those ids.
      * Opition to add new code | Inline discreate form
        * Set code and expiration date only
      * Option to add a new coupon
        * In form:
          * Name,
          * Type | Amount off | Percentage off
          * amount or percentage input
          * code | Capitalize while typing
          * expiration date | Date picker
        * Submiting form | POST /mododeus/coupons
          * creates a new stripe coupon with provided, name, type, amount or percentage
          * creates a new stripe code for that coupon with provided code and expiration date
      * Option to archive a coupon codes | DELETE /mododeus/coupons
        * Archives coupon code from stripe
        * Removes it from displayed list
    * Classes can be:
      * Created
        * Classes form:
          * Name
          * Start At | Date picker
          * End At | Date picker
          * Recurrance: RRULE picker
          * Description | Textarea
          * Level: String input
          * Price: Amount in usd or brazilian real
          * Image: Select image from local machine
              * Upload image to Cloudflare
                * Preferable use one-time upload URL
        * Create class endpoint | POST /mododeus/classes
          * Upload image to Cloudflare via Worker and get its publi url
          * Create a new Stripe product
            * Name
            * Description
            * Product tax code: Educational Services
            * Price | Create stripe price for product
            * Images: Array with url of just uploaded image.
            * Set the following keys in **metadata**:
              * level: level from input
              * recurrence: RRULE string from input
              * start_at: Date from input
              * end_at: Date from input
          * Create a Stripe payment link for product
      * Edited
        * Edit form:
          * Name
          * Start At
          * End At
          * Recurrance: RRULE picker
          * Description
          * Level: String input
          * Image: Select image from local machine
          * Option to delete image
            * Deletes image from Stripe
            * If successful removes it from displayed list
            * Option to add new image
              * Upload image to Cloudflare
                * Preferable use one-time upload URL
              * Add image to Stripe product
                * Images: Array with url of just uploaded image.
      * Updated
        * Update product endpoint | PUT /mododeus/classes/
          * Upload image to Cloudflare and get its public url if image is changed
          * Update Stripe product
            * Name
            * Description
            * Price
            * Images: Array with url of just uploaded image.
            * Set the following keys in **metadata**:
              * level: level from input
              * recurrence: RRULE string from input
              * start_at: Date from input
              * end_at: Date from input
      * Deleted
        * Archive product endpoint | DELETE /mododeus/classes/
* **Index**
  * Visiting "/" displays US store
  * Visiting "/br" displays Brazil store
  * Displays list of active classes
  * This routes are not authenticated
  * Display link to different dance studio
  * Do not display link to `Modo Deus`

**Notes**
* Strikingly display stripe or any errors when they happen
  * Avoid js alert
  * Visually satifiely noticeable
* Important! Only authenticated users can access and make requestes to backend `Modo Deus`
* Write clean and dry code
* Classes are loaded asyncronously on "/" and "/br" endpoints
* Avoid typescript | Use modern javascript
* Avoid vite.js
* Avoid webpack
* Use webcomponents to display list of classes in index pages and mododeus
* Use webcomponents for cupons display and creation

**Test**

* Before testing:
  * Mock google's Oath
  * Mock cloudflare's image upload
  * Besides mocking, make sure to follow actual real behavior

* US store
  * Visit /
  * Clases are displayed from Stripe US
  * Link to BR store is displayed

* BR store
  * Visit /br
  * Clases are displayed from Stripe BR
  * Link to US store is displayed

* Modo Deus
  * Visit /mododeus
  * Verify clases are displayed from Stripe US
  * Select BR store
  * Verify clases are displayed from Stripe BR
  * Create a class
  * Edit a class

* Create classes endpoint
  * Upload image to claudflare successfully
  * Create a stripe product successfully
  * Verify Stripe product is created correctly
  * Verify Scripe payment link is created correctly

* Update classes endpoint
  * Upload new image to claudflare successfully
  * Verify Stripe product is updated acordingly

* Display coupons
  * Display all coupons correctly
  * Create a coupon code correctly
  * Archive a coupon code correctly
    * Successfully archive code on Stripe
    * Coupon code looks deactivated on UI

**Deliverables**

* Create Hono project with cloudflare-pages template
* Create file called context.md
  * Describe the context of the project for next ai to be able to understand what has been done and continue without loossing context
* Create test to ensure app behaves as expected
* Add hight level comments to code for an experienced developer to understand

**For the developer** to get:
* Claudlfare API key | upload images | deploy?
  * CLOUDFLARE_API_KEY
  * CLOUDFLARE_API_TOKEN
  * CLOUDFLARE_ACCOUNT_ID ?
* Google OAuth keys
  * GOOGLE_CLIENT_ID
  * GOOGLE_CLIENT_SECRET
* Stripe api key
  * STRIPE_SECRET_KEY for each studio
