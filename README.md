<!--
# license: Copyright © 2011-2016 MOLPay Sdn Bhd. All Rights Reserved. 
-->

<img src="https://user-images.githubusercontent.com/38641542/39353138-654385dc-4a39-11e8-9710-19e5f03ec62e.jpg">

# molpay-mobile-xdk-xamarin-android

This is the complete and functional MOLPay Xamarin Android payment module that is ready to be implemented into Visual Studio as a MOLPayXDK module. An example application project (MOLPayXdkExample) is provided for MOLPayXDK framework integration reference.

## Recommended configurations

    - Microsoft Visual Studio Community 2015 (For Windows)

    - Package Json.NET

    - Minimum Android API level: 19 ++

    - Minimum Android target version: Android 4.4


## Installation

    Step 1 - In the Solution Explorer of Visual Studio, right click on your Xamarin Android project name and go to Add -> Existing Item..., on the window that pops up, select MOLPayActivity.cs and click Add.

    Step 2 - Copy and paste molpay-mobile-xdk-www folder (can be separately downloaded at https://github.com/MOLPay/molpay-mobile-xdk-www) into the Assets\ folder of your Xamarin Android project.

    Step 3 - Copy and paste custom.css into the Assets\ folder of your Xamarin Android project.

    Step 4 - Copy and paste layout_molpay.axml into the Resources\layout\ folder of your Xamarin Android project. (Create one if the directory does not exist)

    Step 5 - Copy and paste menu_molpay.xml into the Resources\menu\ folder of your Xamarin Android project. (Create one if the directory does not exist)

    Step 6 - In the Solution Explorer of Visual Studio, click the 'Show All Files' button, after that all the files and folders that are pasted just now will be shown. Right click on each of them and click 'Include In Project'.

    Step 7 - Right click on your android project and select Properties. Select Android Manifest in the window that opens. Check WRITE_EXTERNAL_STORAGE in the list of permissions.

    Step 8 - Install Json.NET by going to Tools -> NuGet Package Manager -> Package Manager Console, and run this command 'Install-Package Newtonsoft.Json' (without the quotes) in the console. You may refer to this website http://www.newtonsoft.com/json.

    Step 9 - Override the OnActivityResult function.
    
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        if (requestCode == MOLPayActivity.MOLPayXDK && resultCode == Result.Ok)
        {
            Console.WriteLine("MOLPay result = " + data.GetStringExtra(MOLPayActivity.MOLPayTransactionResult));
            SetContentView(Resource.Layout.layout_molpay);
            TextView tw = (TextView)FindViewById(Resource.Id.resultTV);
            tw.Text = data.GetStringExtra(MOLPayActivity.MOLPayTransactionResult);
        }
    }
    
    =========================================
    Sample transaction result in JSON string:
    =========================================
    
    {"status_code":"11","amount":"1.01","chksum":"34a9ec11a5b79f31a15176ffbcac76cd","pInstruction":0,"msgType":"C6","paydate":1459240430,"order_id":"3q3rux7dj","err_desc":"","channel":"Credit","app_code":"439187","txn_ID":"6936766"}
    
    Parameter and meaning:
    
    "status_code" - "00" for Success, "11" for Failed, "22" for *Pending. 
    (*Pending status only applicable to cash channels only)
    "amount" - The transaction amount
    "paydate" - The transaction date
    "order_id" - The transaction order id
    "channel" - The transaction channel description
    "txn_ID" - The transaction id generated by MOLPay
    
    * Notes: You may ignore other parameters and values not stated above
    
    =====================================
    * Sample error result in JSON string:
    =====================================
    
    {"Error":"Communication Error"}
    
    Parameter and meaning:
    
    "Communication Error" - Error starting a payment process due to several possible reasons, please contact MOLPay support should the error persists.
    1) Internet not available.
    2) API credentials (username, password, merchant id, verify key).
    3) MOLPay server offline.

## Import namespaces

    using MOLPayXDKExample;
    using Newtonsoft.Json;

## Prepare the Payment detail object

    Dictionary<String, object> paymentDetails = new Dictionary<String, object>();
     // Mandatory String. A value more than '1.00'
    paymentDetails.Add(MOLPayActivity.mp_amount, "");

    // Mandatory String. Values obtained from MOLPay
    paymentDetails.Add(MOLPayActivity.mp_username, "");
    paymentDetails.Add(MOLPayActivity.mp_password, "");
    paymentDetails.Add(MOLPayActivity.mp_merchant_ID, "");
    paymentDetails.Add(MOLPayActivity.mp_app_name, "");
    paymentDetails.Add(MOLPayActivity.mp_verification_key, "");

    // Mandatory String. Payment values
    paymentDetails.Add(MOLPayActivity.mp_order_ID, "");
    paymentDetails.Add(MOLPayActivity.mp_currency, "");
    paymentDetails.Add(MOLPayActivity.mp_country, ""); 

    // Optional String.
    paymentDetails.Add(MOLPayActivity.mp_channel, ""); // Use 'multi' for all available channels option. For individual channel seletion, please refer to https://github.com/MOLPay/molpay-mobile-xdk-examples/blob/master/channel_list.tsv. 
    paymentDetails.Add(MOLPayActivity.mp_bill_description, "");
    paymentDetails.Add(MOLPayActivity.mp_bill_name, "");
    paymentDetails.Add(MOLPayActivity.mp_bill_email, "");
    paymentDetails.Add(MOLPayActivity.mp_bill_mobile, "");
    paymentDetails.Add(MOLPayActivity.mp_channel_editing, false); // Option to allow channel selection.
    paymentDetails.Add(MOLPayActivity.mp_editing_enabled, false); // Option to allow billing information editing.

    // Optional for Escrow
    paymentDetails.Add(MOLPayActivity.mp_is_escrow, ""); // Optional for Escrow, put "1" to enable escrow

    // Optional for credit card BIN restrictions
    String[] binlock = new String[] { "", "" };
    paymentDetails.Add(MOLPayActivity.mp_bin_lock, binlock); // Optional for credit card BIN restrictions
    paymentDetails.Add(MOLPayActivity.mp_bin_lock_err_msg, ""); // Optional for credit card BIN restrictions

    // For transaction request use only, do not use this on payment process
    paymentDetails.Add(MOLPayActivity.mp_transaction_id, ""); // Optional, provide a valid cash channel transaction id here will display a payment instruction screen.
    paymentDetails.Add(MOLPayActivity.mp_request_type, ""); // Optional, set 'Status' when performing a transactionRequest

    // Optional, use this to customize the UI theme for the payment info screen, the original XDK custom.css file is provided at Example project source for reference and implementation.
    paymentDetails.Add(MOLPayActivity.mp_custom_css_url, "file:///android_asset/custom.css");

    // Optional, set the token id to nominate a preferred token as the default selection, set "new" to allow new card only
    paymentDetails.Add(MOLPayActivity.mp_preferred_token, "");

    // Optional, credit card transaction type, set "AUTH" to authorize the transaction
    paymentDetails.Add(MOLPayActivity.mp_tcctype, "");

    // Optional, set true to process this transaction through the recurring api, please refer the MOLPay Recurring API pdf 
    paymentDetails.Add(MOLPayActivity.mp_is_recurring, false);

    // Optional for sandboxed development environment, set boolean value to enable
    paymentDetails.Add(MOLPayActivity.mp_sandbox_mode, false);

    // Optional for channels restriction 
    String[] allowedChannels = new String[] { "", "" };
    paymentDetails.Add(MOLPayActivity.mp_allowed_channels, allowedChannels);

    // Optional, required a valid mp_channel value, this will skip the payment info page and go direct to the payment screen.
    paymentDetails.Add(MOLPayActivity.mp_express_mode, false);

    // Optional, enable this for extended email format validation based on W3C standards.
    paymentDetails.Add(MOLPayActivity.mp_advanced_email_validation_enabled, false);

    // Optional, enable this for extended phone format validation based on Google i18n standards.
    paymentDetails.Add(MOLPayActivity.mp_advanced_phone_validation_enabled, false);

    // Optional, explicitly force disable billing name edit.
    paymentDetails.Add(MOLPayActivity.mp_bill_name_edit_disabled, true);

    // Optional, explicitly force disable billing email edit.
    paymentDetails.Add(MOLPayActivity.mp_bill_email_edit_disabled, true);

    // Optional, explicitly force disable billing mobile edit.
    paymentDetails.Add(MOLPayActivity.mp_bill_mobile_edit_disabled, true);

    // Optional, explicitly force disable billing description edit.
    paymentDetails.Add(MOLPayActivity.mp_bill_description_edit_disabled, true);

    // Optional, EN, MS, VI, TH, FIL, MY, KM, ID, ZH.
    paymentDetails.Add(MOLPayActivity.mp_language, "EN");

    // Optional, enable for online sandbox testing.
    paymentDetails.Add(MOLPayActivity.mp_dev_mode, false);

    // Optional, Cash channel payment request expiration duration in hour.
    //paymentDetails.Add(MOLPay.mp_cash_waittime, "48");

    // Optional, allow non-3ds on some credit card channels.
    //paymentDetails.Add(MOLPay.mp_non_3DS, false);

## Start the payment module

    Intent intent = new Intent(this, typeof(MOLPayActivity));
    intent.PutExtra(MOLPayActivity.MOLPayPaymentDetails, JsonConvert.SerializeObject(paymentDetails));
    StartActivityForResult(intent, MOLPayActivity.MOLPayXDK);

## Cash channel payment process (How does it work?)

    This is how the cash channels work on XDK:
    
    1) The user initiate a cash payment, upon completed, the XDK will pause at the “Payment instruction” screen, the results would return a pending status.
    
    2) The user can then click on “Close” to exit the MOLPay XDK aka the payment screen.
    
    3) When later in time, the user would arrive at say 7-Eleven to make the payment, the host app then can call the XDK again to display the “Payment Instruction” again, then it has to pass in all the payment details like it will for the standard payment process, only this time, the host app will have to also pass in an extra value in the payment details, it’s the “mp_transaction_id”, the value has to be the same transaction returned in the results from the XDK earlier during the completion of the transaction. If the transaction id provided is accurate, the XDK will instead show the “Payment Instruction" in place of the standard payment screen.
    
    4) After the user done the paying at the 7-Eleven counter, they can close and exit MOLPay XDK by clicking the “Close” button again.

## XDK built-in checksum validator caveats 

    All XDK come with a built-in checksum validator to validate all incoming checksums and return the validation result through the "mp_secured_verified" parameter. However, this mechanism will fail and always return false if merchants are implementing the private secret key (which the latter is highly recommended and prefereable.) If you would choose to implement the private secret key, you may ignore the "mp_secured_verified" and send the checksum back to your server for validation. 

## Private Secret Key checksum validation formula

    chksum = MD5(mp_merchant_ID + results.msgType + results.txn_ID + results.amount + results.status_code + merchant_private_secret_key)

## Support

Submit issue to this repository or email to our support@molpay.com

Merchant Technical Support / Customer Care : support@molpay.com<br>
Sales/Reseller Enquiry : sales@molpay.com<br>
Marketing Campaign : marketing@molpay.com<br>
Channel/Partner Enquiry : channel@molpay.com<br>
Media Contact : media@molpay.com<br>
R&D and Tech-related Suggestion : technical@molpay.com<br>
Abuse Reporting : abuse@molpay.com
