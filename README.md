my_work
=======
small app to login with gmail/google plus and display the contacts of the user after authentication.
home.php
================


<!DOCTYPE html>
<html>
<head>

<meta name="google-signin-clientid" content="957586932637-5i9u5ebvsbdjti00t1o5qug60jobc5ol.apps.googleusercontent.com" />
<meta name="google-signin-scope" content="https://www.googleapis.com/auth/plus.login" />
<meta name="google-signin-requestvisibleactions" content="http://schema.org/AddAction" />
<meta name="google-signin-cookiepolicy" content="single_host_origin" />



<script type="text/javascript">

  (function() {

    var plus1 = document.createElement('script');

    plus1.type = 'text/javascript';
    plus1.async = true;

    plus1.src = 'https://plus.google.com/js/client:plusone.js';

    var s = document.getElementsByTagName('script')[0];

    s.parentNode.insertBefore(plus1, s);
  })();

  </script>


<!-- JavaScript specific to this application that is not related to API
     calls -->
  
<script src="//ajax.googleapis.com/ajax/libs/jquery/1.8.2/jquery.min.js" ></script>


<title>Home</title>

  
</head>

<body>
<!--<button id="signinButton" type="submit" name="submit" value="Sign in with Gmail">Sign in with Gmail</button>
-->
<!--<a href= "google_login.php">
	<input type="submit" name="submit" value="login with gmail">
</a>
-->
<body>

  <div id="gConnect">

    <button class="g-signin"

        data-scope="https://www.googleapis.com/auth/plus.login"
 
        data-requestvisibleactions="http://schemas.google.com/AddActivity"
     
   	data-clientId="957586932637-5i9u5ebvsbdjti00t1o5qug60jobc5ol.apps.googleusercontent.com"

        data-callback="onSignInCallback"

        data-theme="dark"

        data-cookiepolicy="single_host_origin">
   
 </button>

  </div>


 <div id="authOps" style="display:none">

    
    <h4>Your profile information</h4>
    
<div id="profile"></div>

 
   <h4>Your friends that are visible to this app</h4>
    

<div id="visiblePeople"></div>

    

<h4>You are now signed in to the app using Google+</h4>

    <p>If you chooses to disconnect, the app must delete all stored
    information retrieved from Google about you.</p>
    
<button id="disconnect" >Disconnect from this app</button>


</body>


<?php
static $people="hi";
// echo "$people";
?>

<script type="text/javascript">

var helper = (function() {
  var BASE_API_PATH = 'plus/v1/';

  return {
   
/**

* Hides the sign in button and starts the post-authorization operations.
     
*
     
* @param {Object} authResult An Object which contains the access token and
     
*   other authentication information.
     */
   
 
onSignInCallback: function(authResult) {

      gapi.client.load('plus','v1', function(){

        $('#authResult').html('Auth Result:<br/>');

        for (var field in authResult) {

          $('#authResult').append(' ' + field + ': ' +
              authResult[field] + '<br/>');

        }

        if (authResult['access_token']) {

          $('#authOps').show('slow');

          $('#gConnect').hide();

          helper.profile();

          helper.people();

        } else if (authResult['error']) {

          // There was an error, which means the user is not signed in.

          // As an example, you can handle by writing to the console:

          console.log('There was an error: ' + authResult['error']);

          $('#authResult').append('Logged out');

          $('#authOps').hide('slow');

          $('#gConnect').show();

        }

        console.log('authResult', authResult);

      });

    },

   
 /**

     * Calls the OAuth2 endpoint to disconnect the app for the user.

     */

    disconnect: function() {

      // Revoke the access token.

      $.ajax({
        type: 'GET',
        url: 'https://accounts.google.com/o/oauth2/revoke?token=' +

            gapi.auth.getToken().access_token,
        async: false,
        contentType: 'application/json',
        dataType: 'jsonp',
        
           success: function(result) {

          console.log('revoke response: ' + result);

          $('#authOps').hide();

          $('#profile').empty();

          $('#visiblePeople').empty();

          $('#authResult').empty();

          $('#gConnect').show();

        },

        error: function(e) {

          console.log(e);

        }

      });

    },


    /**

     * Obtains and renders the list of people visible to the app.

     */



    people: function() {

      var request = gapi.client.plus.people.list({

        'userId': 'me',
        'collection': 'visible'
      });

      request.execute(function(people) {

        $('#visiblePeople').empty();

        $('#visiblePeople').append('Number of people visible to this app: ' +
            people.totalItems + '<br/>');

	
	var x=1;
	for (var personIndex in people.items) {

          person = people.items[personIndex];

	
	         $('#visiblePeople').append((x++)+" "+person.displayName+" "+'<img src="'+person.image.url+'">'+"<br/> ");

        }

	
		
      });

    },

 
  

    /**

     * Gets and renders the currently signed in user's profile data.

     */

    profile: function(){

      var request = gapi.client.plus.people.get( {'userId' : 'me'} );

      request.execute( function(profile) {

        $('#profile').empty();

        if (profile.error) {

          $('#profile').append(profile.error);

          return;

        }

        $('#profile').append(
            $('<p><img src=\"' + profile.image.url + '\"></p>'));

        $('#profile').append(
            $('<p>Hello ' + profile.displayName + '!<br />Tagline: ' +
            profile.tagline + '<br />About: ' + profile.aboutMe + '</p>'));

        if (profile.cover && profile.coverPhoto) {
          $('#profile').append(
              $('<p><img src=\"' + profile.cover.coverPhoto.url + '\"></p>'));
        }
      });

    }
  };
})();


/**
 * jQuery initialization
 */

$(document).ready(function() {
  $('#disconnect').click(helper.disconnect);

  $('#loaderror').hide();

  if ($('[data-clientid="YOUR_CLIENT_ID"]').length > 0) {

    alert('This sample requires your OAuth credentials (client ID) ' +
        'from the Google APIs console:\n' +
        '    https://code.google.com/apis/console/#:access\n\n' +
        'Find and replace YOUR_CLIENT_ID with your client ID.'
    );

  }
});


/**
 * Calls the helper method that handles the authentication flow.
 
*
 * @param {Object} authResult An Object which contains the access token and
 
*   other authentication information.
 */

function onSignInCallback(authResult) {

  helper.onSignInCallback(authResult);

}


</script>



</html>
