  <h1>How to Embed Friendly Captcha in SoSci Survey</h1>

  <p>A Step-by-Step Integration Guide: Sarah Foeller</p>

  <h3>Overview</h3>

  <p>Friendly Captcha a DSGVO-compliant bot protection service developed by a German
  company. It uses a proof-of-work mechanism that runs invisibly in the user's browser —
  real participants see nothing, and bots are blocked before they can submit. Unlike
  Google reCAPTCHA, no personal data (IP address, browser fingerprint) is transmitted to
  third-party servers located outside of the EU.</p>

  <h3>Short Guide</h3>

  <p>The implementation of Friendly Captcha now embedded in SoSci Survey:</p>

  <p><em>(English)</em></p>

  <ol>
    <li>questions → add question</li>
    <li>chose „Captcha“</li>
    <li>type → „FriendlyCaptcha“</li>
    <li>enter Sitekey and API-Key (see step 1)</li>
    <li>pull Captcha on desired questionnaire page</li>
  </ol>

  <p><em>(German)</em></p>

  <ol>
    <li>Im Fragenkatalog → Frage hinzufügen.</li>
    <li>Fragetyp „Captcha“ auswählen.</li>
    <li>Bei Art des Captchas → „FriendlyCaptcha“ auswählen.</li>
    <li>Deinen Sitekey und API-Key eintragen (step 1).</li>
    <li>Diese Captcha-Frage auf die gewünschte Fragebogenseite ziehen.</li>
  </ol>

  <h3>Step by Step</h3>

  <h4>STEP 1 — Create a Friendly Captcha Account</h4>

  <p><em>Obtain your API credentials from the Friendly Captcha dashboard</em></p>

  <p>Go to <a href="https://app.friendlycaptcha.eu/dashboard">https://app.friendlycaptch
  a.eu/dashboard</a> and register for a free account.</p>

  <p>The free plan allows up to 1,000 verification requests per month, which is
  sufficient for most academic studies. After registration, you need two
  credentials:</p>

  <ol>
    <li><strong>Sitekey</strong> — a public identifier placed in your HTML widget. It
  tells Friendly Captcha which account to use.</li>
    <li><strong>API Key</strong> — a secret key used server-side in your PHP code to
  verify tokens. Never share or expose this key publicly.</li>
  </ol>

  <h4>STEP 2 — Add the Captcha Widget (HTML Text Element)</h4>

  <p><em>Embed the Friendly Captcha widget and token-copy script on your survey
  page</em></p>

  <p>In SoSci Survey, create a new HTML-Code text element (I recommend adding it in a
  question block) and then drag it to the survey page where you want to check for bots.
  The HTML-Code should read like so:</p>

  <pre><code>&lt;!-- Load the Friendly Captcha widget --&gt;
  &lt;script type="module"
      src="https://cdn.jsdelivr.net/npm/@friendlycaptcha/sdk@0.1.36/site.min.js"
      async defer&gt;
  &lt;/script&gt;

  &lt;script nomodule
      src="https://cdn.jsdelivr.net/npm/@friendlycaptcha/sdk@0.1.36/site.compat.min.js"
      async defer&gt;
  &lt;/script&gt;

  &lt;div class="frc-captcha"
      data-sitekey="YOUR_SITEKEY_HERE"&gt;
  &lt;/div&gt;

  &lt;!-- Copy frc-captcha-response token into SoSci internal variable --&gt;
  &lt;script type="text/javascript"&gt;
  &lt;!--
  document.querySelector('.frc-captcha').addEventListener('frc:widget.complete',
  function(e) {
      var input = document.getElementById("A702_01");
      if (input) {
          input.value = e.detail.response;
      }
  });
  // --&gt;
  &lt;/script&gt;
  </code></pre>

  <p>Replace YOUR_SITEKEY_HERE with your actual Sitekey from the Friendly Captcha
  dashboard. The second JavaScript listens for the moment the captcha puzzle is solved
  and copies the resulting token into the hidden form field created by your internal
  variable question: Replace A702_01 with the actual identifier of your internal
  variable.</p>

  <blockquote>
    <p>ℹ For DSGVO compliance, download the java script from each website and safe them
  with the ending .js:</p>
    <ul>
      <li>https://cdn.jsdelivr.net/npm/@friendlycaptcha/sdk@0.1.36/site.min.js (save as
  site.min.js)</li>
      <li>https://cdn.jsdelivr.net/npm/@friendlycaptcha/sdk@0.1.36/site.compat.min.js
  (save as site.compat.min.js)</li>
    </ul>
    <p>Then upload them in SoSciSurvey: Bilder und Mediendateien -&gt; Mediandateien
  hochladen they should both be around 30 KB. Now use this code instead, it loads the
  java script from soscisurvey instead of making a request to cdn.jsdelivr.net (USA)
  servers.</p>
  </blockquote>

  <pre><code>&lt;!-- Load Friendly Captcha widget (self-hosted for GDPR compliance)
  --&gt;
  &lt;script type="module"
      src="https://www.soscisurvey.de/StressWork2Anmeldung/files/site.min.js"
      async defer&gt;
  &lt;/script&gt;

  &lt;script nomodule
      src="https://www.soscisurvey.de/StressWork2Anmeldung/files/site.compat.min.js"
      async defer&gt;
  &lt;/script&gt;
  </code></pre>

  <p>Replace StressWork2Anmeldung with your actual project link.</p>

  <h4>STEP 3 — Create the Internal Variable Question in SoSciSurvey</h4>

  <p><em>Set up the hidden storage field that transfers the captcha token to
  PHP</em></p>

  <p>In SoSci Survey create a new question block and create a internal variable, go to:
  Fragenkatalog → Frage hinzufügen → Funktionale Bausteine → Interne Variablen</p>

  <p>Then name the first item in the newly created internal variable. In this example we
  named it CAPT — this becomes the column header in your exported dataset. This first
  variable now has an assigned variable identifier:</p>

  <pre><code>Variable identifier: A702_01
    (or whatever SoSci assigns — check under Variablen-Übersicht)
  </code></pre>

  <blockquote>
    <p>⚠ <strong>Important:</strong> The internal variable question must be dragged onto
  the same survey PAGE as the captcha widget later. This is what causes SoSci to render
  a hidden <code>&lt;input&gt;</code> field in the HTML form, which JavaScript can then
  write the token into.</p>
  </blockquote>

  <p><strong>Page Layout After Step 3</strong></p>

  <p>Your survey page should now contain, in this order:</p>

  <ol>
    <li>HTML-Code text element: widget loading scripts + captcha div + JavaScript
  token-copy script</li>
    <li>Internal variable question (A702 / CAPT) — dragged onto this page</li>
  </ol>

  <h4>STEP 4 — Add PHP Verification on the Next Page</h4>

  <p><em>Verify the token with Friendly Captcha's API and block bots</em></p>

  <p>On the page immediately following your captcha page, add a PHP text element with
  the following code:</p>

  <pre><code>// Read the captcha token from the internal variable (submitted on previous
  page)
  $captcha_response = value('A702_01');

  // Send the token to Friendly Captcha's EU API for verification
  $result = sendJSON(
    'https://eu.frcapi.com/api/v2/captcha/siteverify',
    [
      'response'  =&gt; $captcha_response,
      'sitekey'   =&gt; 'YOUR_SITEKEY_HERE'
    ],
    [
      'X-API-Key' =&gt; 'YOUR_API_KEY_HERE'
    ]
  );

  // Block navigation if verification failed not possible to continue (klick next page)
  if ($result['success'] == false) {
    option('backbutton', false);
    option('nextbutton', false);
  }
  </code></pre>
  
  <p>Replace YOUR_SITEKEY_HERE and YOUR_API_KEY_HERE with your actual credentials from
  the Friendly Captcha dashboard. Replace A702_01 with the actual identifier of your
  internal variable.</p>

  <blockquote>
    <p>⚠ <strong>Why the PHP goes on the NEXT page:</strong> SoSci executes PHP when
  building a page, before the user has submitted the form. So value('A702_01') can only
  be read after the form has been submitted — i.e., on the following page.</p>
  </blockquote>

  <p>Alternative to just blocking the next and back button: Flag if participant was a
  bot by creating a new item (e.e. A702_02) and set the value 1 everytime it is a bot
  and send them to an error page.</p>

  <pre><code>// Block navigation if verification failed not possible to continue (klick
  next page)
  if ($result['success'] == false) {
      put('A702_02', 1); // flag in dataset (bot = 1)
      goToPage(error); // repeat captcha
  }
  </code></pre>

  <blockquote>
    <p>⚠ <strong>Take care:</strong> SoSciSurvey only runs sendJSON() twice. As a result
  the thrid time answering the friendly captcha bots can pass on. They therefore should
  be blocked after the second trial.</p>
  </blockquote>

  <blockquote>
    <p>ℹ You can also paste your own PHP code in the if($result['success'] == false) {}
  block. The one above stops the page from showing the next or back button.</p>
  </blockquote>

  <p><strong>During Testing: Use Debug Mode</strong></p>

  <p>While testing, add these two lines at the end of your PHP block to inspect the
  values:</p>

  <pre><code>debug($captcha_response); // Should show a long token string starting with
  AQEA... or AQQA...
  debug($result);           // Should show [success][true] for a real person
  </code></pre>

  <h3>What Appears in Your Dataset</h3>

  <p>After data collection, the column labelled CAPT (or whatever item label you chose)
  will contain the raw Friendly Captcha token string for each submission. Example:</p>

  <pre><code>AQQA.8ZFE5oTTEfRjBlMxg6y1p_W2hLQjMVe7JsAFKx-nFX9OUtwkf5bEdyw...
  </code></pre>

  <p>A valid token is long (several hundred characters) and starts with AQEA or AQQA.
  You can use this to manually identify and exclude any dataset rows where the token is
  empty or suspiciously short — which would indicate the captcha was bypassed.</p>

  <h3>Summary: How It Works</h3>

  <p>The full flow when a real participant opens the captcha page:</p>

  <ol>
    <li><strong>Widget loads and solves puzzle.</strong> When the page opens, the
  Friendly Captcha widget silently runs a proof-of-work computation in the user's
  browser. This takes a few seconds and requires no user interaction.</li>
    <li><strong>Token is generated.</strong> Once complete, the widget fires an
  frc:widget.complete event and stores a signed token string (e.g. AQQA....) in a hidden
  form field.</li>
    <li><strong>JavaScript copies token to SoSci.</strong> The event listener in your
  HTML block catches this event and writes the token into the hidden input field of the
  internal variable (A702_01).</li>
    <li><strong>Form is submitted.</strong> When the participant clicks "Weiter", SoSci
  submits the form including the token stored in A702_01. This token is now saved in the
  dataset.</li>
    <li><strong>PHP verifies on the next page.</strong> On page 2, the PHP code reads
  the token via value('A702_01') and sends it to Friendly Captcha's EU API using
  sendJSON(). The API responds with [success][true] for real humans.</li>
    <li><strong>Bots are redirected.</strong> If verification fails (bot, invalid token,
  or expired token), the participant is silently redirected to the 'bot' page and
  cannot continue.</li>
  </ol>

<h3>Troubleshooting: Error / Symptom — Cause &amp; Fix</h3>

response_invalid (error 40501)	
The token sent was empty or a literal string. Check: 
    (1) that value() is called on the NEXT page, not the same page. 
    (2) that A702_01 is dragged onto the captcha page. 
    (3) no quotes around the variable in the 'response' field.

value('A702_01') = empty string	
The JavaScript event listener fired before the internal variable's hidden input field was rendered. Ensure the internal variable question is placed on the page BEFORE the JS block, or use a DOMContentLoaded wrapper.

Die Variable A702_01 gibt es nicht	
You used value() on the same page as the widget. Move all PHP verification to the next page.

Die Variable A702_01 wurde auf keiner vorhergehenden Seite abgefragt	
Same cause as above — PHP is running on the same page as the form. Move the PHP block to the next page.
[success][false] for real participants	The token may have expired (tokens are valid for ~2 minutes). If participants take too long to click 'Weiter', the token times out. Consider only blocking if isset($result['success']) is true.

sendJSON() returns false	
Network error — SoSci could not reach the Friendly Captcha API. The isset() check in the production code handles this gracefully by allowing participants through rather than blocking everyone.
