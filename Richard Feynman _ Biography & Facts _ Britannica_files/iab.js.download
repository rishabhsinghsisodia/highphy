
// ----------------------------------------------------------------------------------------------------------------------------
// --- utilities
// ----------------------------------------------------------------------------------------------------------------------------

const US_PrivacyCookieName = 'usprivacy';
const US_PrivacyCookieDuration = 365; // days

var __iab = {

    // ---------------------------------------
    // --- checks if the privacy cookie exists
    // ---------------------------------------

    cookieExists: function() {
        return document.cookie.split(';').filter((item) => item.includes(US_PrivacyCookieName + '=')).length > 0;
    },

    // --------------------------
    // -- Read the privacy cookie
    // --------------------------

    getCookie: function () {
        var cookieName = US_PrivacyCookieName + "=";
        var theCookies = "; " + document.cookie;
        var cookieArray = theCookies.split(';');
        for (var i = 0; i < cookieArray.length; i++) {
            var cookie = cookieArray[i];
            while (cookie.charAt(0) === ' ') {
                cookie = cookie.substring(1, cookie.length);
                if (cookie.indexOf(cookieName) === 0) {
                    return cookie.substring(cookieName.length, cookie.length);
                }
            }
        }
        return null;
    },

    // ---------------------------------------------------------------------------
    // --- Create the privacy cookie
    //     The value of the cookie/usprivacy string should be a 4 character string
    //     char 1 {version} = 1
    //     char 2 {notice} = Y, N, -
    //     char 3 {consent} = Y, N, -
    //     char 4 {LSPA agreements} - Y, N, -
    // ---------------------------------------------------------------------------

    createCookie: function ( version, notice, optOutSale, lspa ) {

        var charValue = function( c ) { return c === true ? 'Y' : c === false ? 'N' : '-';  }

        var value = ( version || '1' ) + charValue( notice ) + charValue( optOutSale ) + charValue( lspa );

   	    var samesiteString = 'SameSite=Lax; ',
   	        secureString = '',
   	        pathString = 'path=/; ',
   	        nameString = US_PrivacyCookieName + '=' + (value || '') + '; ';
   	        var date = new Date();
   	        date.setTime(date.getTime() + (US_PrivacyCookieDuration * 24 * 60 * 60 * 1000));
   	        var expireString = 'expires=' + date.toUTCString() + '; ';

   	        document.cookie = nameString + expireString + pathString + samesiteString + secureString;
   	},

    // ----------------------
    // -- iab message handler
    // ----------------------

    optOutMsgHandler: function (event) {

        var msgIsString = typeof event.data === "string";
        var json;

        if (msgIsString) {
            json = event.data.indexOf("__uspapiCall") !== -1 ? JSON.parse(event.data) : {};
        } else {
            json = event.data;
        }

        if (json.__uspapiCall) {
            var i = json.__uspapiCall;
            window.__uspapi(i.command, i.version, function (retValue, success) {
                var returnMsg = {
                    "__uspapiReturn": {
                        "returnValue": retValue,
                        "success": success,
                        "callId": i.callId
                    }
                };
                event.source.postMessage(msgIsString ? JSON.stringify(returnMsg) : returnMsg, '*');
            });
        }
    }
};

window.addEventListener('message', __iab.optOutMsgHandler, false);

//US Privacy String API - required by the IAB to be a specific format
/** @function __uspapi
 *   @summary This function provides an api for vendors to access a user's us privacy string data
 *   @param {String} command - A command name
 *   @param {Number} version - The us privacy string version number
 *   @param {Function} callback - A callback function accepting usprivacy object as the first param, and success as the second
 *   @description A lengthy description of the purpose of this function
 *   @references https://iabtechlab.com
 *   @example
 *   __uspapi('getUSPData', 1, (data, success) =>{console.log(data, success) });
 *   > {version: 1, uspString: null}, false
 */

window.__uspapi = function(command, version, callback) {

    switch( command.toLowerCase() ) {

        case 'getuspdata':
            var privacyObject = { 'version': version, 'uspString': __iab.getCookie() };
            callback( privacyObject, privacyObject.uspString != null );
            break;
    }
};

// Initializes cookie

if  ( !__iab.cookieExists() ) {

    if ( Mendel.config.userInfo.privacyInfo.ccpa ) {
        __iab.createCookie( 1, true, false, true );
    }
    else {
        __iab.createCookie();
    }
}