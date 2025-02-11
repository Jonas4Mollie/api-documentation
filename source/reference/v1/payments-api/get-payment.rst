Get payment
===========
.. api-name:: Payments API
   :version: 1

.. warning:: The v1 API has been deprecated. The v1 API will be supported for the foreseeable future, at least until
             July 2023. However, new features will only be added to the v2 API.

             The documentation for retrieving payments in the new v2 API can be found
             :doc:`here </reference/v2/payments-api/get-payment>`. For more information on the v2 API, refer to our
             :doc:`v2 migration guide </payments/migrating-v1-to-v2>`.

.. endpoint::
   :method: GET
   :url: https://api.mollie.com/v1/payments/*id*

.. authentication::
   :api_keys: true
   :organization_access_tokens: false
   :oauth: true

Retrieve a single payment object by its payment token.

.. note:: We call your webhook when the :doc:`payment status changes </payments/status-changes>`, so there's no
          need to poll this endpoint for status changes.

Parameters
----------
Replace ``id`` in the endpoint URL by the payment's ID, for example ``tr_7UhSN1zuXS``.

Access token parameters
^^^^^^^^^^^^^^^^^^^^^^^
If you are using :doc:`organization access tokens </overview/authentication>` or are creating an
:doc:`OAuth app </connect/overview>`, the ``testmode`` query string parameter is available. You must pass this as a
parameter in the query string if you want to retrieve a payment that was created in test mode.

.. parameter:: testmode
   :type: boolean
   :condition: optional
   :collapse: true

   Set this to ``true`` to get a payment made in test mode. If you omit this parameter, you can only retrieve live mode
   payments.

Includes
^^^^^^^^
This endpoint allows you to include additional information by appending the following values via the ``include``
querystring parameter.

* ``settlement`` Include the settlement this payment belongs to, when available.
* ``details.qrCode`` Include a :doc:`QR code </payments/qr-codes>` object. Only available for iDEAL, Bancontact
  and bank transfer payments.

Response
--------
``200`` ``application/json``

.. parameter:: resource
   :type: string

   Indicates the response contains a payment object. Will always contain ``payment`` for this endpoint.

.. parameter:: id
   :type: string

   The identifier uniquely referring to this payment. Mollie assigns this identifier at payment creation time. For
   example ``tr_7UhSN1zuXS``. Its ID will always be used by Mollie to refer to a certain payment.

.. parameter:: mode
   :type: string

   The mode used to create this payment. Mode determines whether a payment is *real* (live mode) or a *test* payment.

   Possible values: ``live`` ``test``

.. parameter:: createdDatetime
   :type: datetime

   The payment's date and time of creation, in `ISO 8601 <https://en.wikipedia.org/wiki/ISO_8601>`_ format.

.. parameter:: status
   :type: string

   The payment's status. Please refer to the documentation regarding statuses for more info about which statuses occur
   at what point.

.. parameter:: isCancelable
   :type: boolean

   Whether or not the payment can be canceled.

.. parameter:: paidDatetime
   :type: datetime

   The date and time the payment became paid, in `ISO 8601 <https://en.wikipedia.org/wiki/ISO_8601>`_ format. This
   parameter is omitted if the payment isn't completed (yet).

.. parameter:: cancelledDatetime
   :type: datetime

   The date and time the payment was canceled, in `ISO 8601 <https://en.wikipedia.org/wiki/ISO_8601>`_ format. This
   parameter is omitted if the payment isn't canceled (yet).

.. parameter:: expiredDatetime
   :type: datetime

   The date and time the payment was expired, in `ISO 8601 <https://en.wikipedia.org/wiki/ISO_8601>`_ format. This
   parameter is omitted if the payment did not expire (yet).

.. parameter:: expiryPeriod
   :type: duration

   The time until the payment will expire in `ISO 8601 duration <https://en.wikipedia.org/wiki/ISO_8601#Durations>`_
   format.

.. parameter:: failedDatetime
   :type: datetime

   The date and time the payment failed, in `ISO 8601 <https://en.wikipedia.org/wiki/ISO_8601>`_ format. This parameter
   is omitted if the payment did not fail (yet).

.. parameter:: amount
   :type: decimal

   The amount in EUR.

.. parameter:: amountRefunded
   :type: decimal

   The total amount that is already refunded. Only available when refunds are available for this payment. For some
   payment methods, this amount may be higher than the payment amount, for example to allow reimbursement of the costs
   for a return shipment to the customer.

.. parameter:: amountRemaining
   :type: decimal

   The remaining amount that can be refunded. Only available when refunds are available for this payment.

.. parameter:: description
   :type: string

   A short description of the payment. The description is visible in the Dashboard and will be shown on the customer's
   bank or card statement when possible.

.. parameter:: method
   :type: string

   The payment method used for this payment, either forced on creation by specifying the ``method`` parameter, or chosen
   by the customer on our payment method selection screen.

   If the payment is only partially paid with a gift card, the method remains ``giftcard``.

   Possible values: ``null`` ``banktransfer`` ``belfius`` ``creditcard`` ``directdebit`` ``eps``, ``giftcard``
   ``giropay`` ``ideal`` ``kbc`` ``klarnapaylater`` ``klarnapaynow`` ``klarnasliceit`` ``mistercash`` ``mybank`` ``paypal``
   ``paysafecard`` ``przelewy24`` ``sofort``

.. parameter:: metadata
   :type: mixed

   The optional metadata you provided upon payment creation. Metadata can for example be used to link an order to a
   payment.

.. parameter:: locale
   :type: string

   The customer's locale, either forced on creation by specifying the ``locale`` parameter, or detected by us during
   checkout. Will be a full locale, for example ``nl_NL``.

.. parameter:: countryCode
   :type: string

   The customer's `ISO 3166-1 alpha-2 <https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2>`_ country code, detected by us
   during checkout. For example: ``BE``.

.. parameter:: profileId
   :type: string

   The identifier referring to the profile this payment was created on. For example, ``pfl_QkEhN94Ba``.

.. parameter:: settlementId
   :type: string

   The identifier referring to the settlement this payment was settled with. For example, ``stl_BkEjN2eBb``.

.. parameter:: issuer
   :type: string

   Only available for payment methods that use an issuer, e.g. iDEAL, KBC/CBC payment button and gift cards. Holds the
   ID of the issuer that was used during the payment.

.. parameter:: failureReason
   :type: string

   Only available for failed Bancontact and credit card payments. Contains a failure reason code.

   Possible values: ``authentication_abandoned`` ``authentication_failed`` ``authentication_required``
   ``authentication_unavailable_acs`` ``card_declined`` ``card_expired`` ``inactive_card``
   ``insufficient_funds`` ``invalid_cvv`` ``invalid_card_holder_name`` ``invalid_card_number``
   ``invalid_card_type`` ``possible_fraud`` ``refused_by_issuer`` ``unknown_reason``

.. parameter:: links
   :type: object

   An object with several URLs important to the payment process.

   .. parameter:: paymentUrl
      :type: string
      :condition: optional

      The URL your customer should visit to make the payment. This is where you should redirect the consumer to.

      .. note:: You should use HTTP ``GET`` for the redirect to the checkout URL. Using HTTP ``POST`` for redirection
         will cause issues with some payment methods or iDEAL issuers. Use HTTP status code ``303 See Other`` to force
         an HTTP ``GET`` redirect.

      Recurring payments do not have a ``paymentUrl``.

   .. parameter:: webhookUrl
      :type: string
      :condition: optional

      The URL Mollie will call as soon an important status change takes place.

   .. parameter:: redirectUrl
      :type: string

      The URL your customer will be redirected to after completing or canceling the payment process.

      .. note:: The URL will be ``null`` for recurring payments.

   .. parameter:: settlement
      :type: string
      :condition: optional

      The API resource URL of the settlement this payment belongs to.

   .. parameter:: refunds
      :type: string
      :condition: optional

      The API resource URL of the refunds that belong to this payment.

   .. parameter:: chargebacks
      :type: string
      :condition: optional

      The API resource URL of the chargebacks that belong to this payment.

Response parameters for recurring payments
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. parameter:: recurringType
   :type: string
   :collapse: true

   This field indicates the position of the payment in a recurring stream. Refer to the
   :doc:`recurring payments guide </payments/recurring>` for more information.

   Possible values: ``null`` ``first`` ``recurring``

.. parameter:: mandateId
   :type: string
   :collapse: true

   If the payment is a first or recurring payment, this field will hold the ID of the mandate.

.. parameter:: customerId
   :type: string
   :collapse: true

   If a customer was specified upon payment creation, the customer's token will be available here as well. For example,
   ``cst_XPn78q9CfT``. When the customer has been deleted this property will still be set.

.. parameter:: subscriptionId
   :type: string
   :collapse: true

   When implementing the Subscriptions API, any recurring charges resulting from the subscription will hold the ID of
   the subscription that triggered the payment.

Payment method-specific response parameters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
If the payment has been created with a ``method``, or if the customer selected a method in the payment method selection
screen, a ``details`` object becomes available on the payment object. This object contains detail fields specific to the
selected payment method.

Bancontact
""""""""""
.. parameter:: details
   :type: object
   :collapse-children: false

   An object with payment details.

   .. parameter:: cardNumber
      :type: string

      Only available if the payment is completed - The last four digits of the card number.

   .. parameter:: cardFingerprint
      :type: string

      Only available if the payment is completed - Unique alphanumeric representation of card, usable for identifying
      returning customers.

      .. warning:: The fingerprint is now (as of November 28th, 2019) unique per transaction what makes it not useful
         anymore for identifying returning customers. Use the ``consumerAccount`` field instead.

   .. parameter:: qrCode
      :type: object

      Only available if requested during payment creation - The QR code that can be scanned by the mobile Bancontact
      application. This enables the desktop to mobile feature.

   .. parameter:: consumerName
      :type: string

      Only available if the payment is completed – The consumer's name.

   .. parameter:: consumerAccount
      :type: string

      Only available if the payment is completed – The consumer's bank account. This may be an IBAN, or it may be a
      domestic account number.

   .. parameter:: consumerBic
      :type: string

      Only available if the payment is completed – The consumer's bank's BIC / SWIFT code.

Bank transfer
"""""""""""""
.. parameter:: details
   :type: object
   :collapse-children: false

   An object with payment details.

   .. parameter:: bankName
      :type: string

      The name of the bank the consumer should wire the amount to.

   .. parameter:: bankAccount
      :type: string

      The IBAN the consumer should wire the amount to.

   .. parameter:: bankBic
      :type: string

      The BIC of the bank the consumer should wire the amount to.

   .. parameter:: transferReference
      :type: string

      The reference the consumer should use when wiring the amount. Note you should not apply any formatting here; show
      it to the consumer as-is.

   .. parameter:: consumerName
      :type: string

      Only available if the payment has been completed – The consumer's name.

   .. parameter:: consumerAccount
      :type: string

      Only available if the payment has been completed – The consumer's bank account. This may be an IBAN, or it may be
      a domestic account number.

   .. parameter:: consumerBic
      :type: string

      Only available if the payment has been completed – The consumer's bank's BIC / SWIFT code.

   .. parameter:: billingEmail
      :type: string

      Only available if filled out in the API or by your customer – The email address which your customer asked the
      payment instructions to be sent to.

Belfius Pay Button
""""""""""""""""""
.. parameter:: details
   :type: object
   :collapse-children: false

   An object with payment details.

   .. parameter:: consumerName
      :type: string

      Only available one banking day after the payment has been completed – The consumer's name.

   .. parameter:: consumerAccount
      :type: string

      Only available one banking day after the payment has been completed – The consumer's bank account's IBAN.

   .. parameter:: consumerBic
      :type: string

      Only available one banking day after the payment has been completed – ``GKCCBEBB``.

.. _Credit card v1:

Credit card
"""""""""""
.. parameter:: details
   :type: object
   :collapse-children: false

   An object with payment details.

   .. parameter:: cardHolder
      :type: string

      Only available if the payment has been completed - The card holder's name.

   .. parameter:: cardNumber
      :type: string

      Only available if the payment has been completed - The last four digits of the card number.

   .. parameter:: cardFingerprint
      :type: string

      Only available if the payment has been completed - Unique alphanumeric representation of card, usable for
      identifying returning customers.

   .. parameter:: cardAudience
      :type: string

      Only available if the payment has been completed and if the data is available - The card's target audience.

      Possible values: ``consumer`` ``business`` ``null``

   .. parameter:: cardLabel
      :type: string

      Only available if the payment has been completed - The card's label. Note that not all labels can be processed
      through Mollie.

      Possible values: ``American Express`` ``Carta Si`` ``Carte Bleue`` ``Dankort`` ``Diners Club`` ``Discover``
      ``JCB`` ``Laser`` ``Maestro`` ``Mastercard`` ``Unionpay`` ``Visa`` ``null``

   .. parameter:: cardCountryCode
      :type: string

      Only available if the payment has been completed - The
      `ISO 3166-1 alpha-2 <https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2>`_ country code of the country the card was
      issued in. For example: ``BE``.

   .. parameter:: cardSecurity
      :type: string

      Only available if the payment has been completed – The type of security used during payment processing.

      Possible values: ``normal`` ``3dsecure``

   .. parameter:: feeRegion
      :type: string

      Only available if the payment has been completed: the fee region for the payment. The ``intra-eu`` value is for
      consumer cards from the EEA.

      Possible values: ``american-express`` ``amex-intra-eea`` ``carte-bancaire`` ``intra-eu`` ``intra-eu-corporate``
      ``domestic`` ``maestro`` ``other``

EPS
"""
.. parameter:: details
   :type: object
   :collapse-children: false

   An object with payment details.

   .. parameter:: consumerName
      :type: string

      Generally only available one banking day after the payment has been completed – The consumer's name.

   .. parameter:: consumerAccount
      :type: string

      Generally only available one banking day after the payment has been completed – The consumer's bank account IBAN.

   .. parameter:: consumerBic
      :type: string

      Generally only available one banking day after the payment has been completed. The consumer's bank's BIC.

Gift cards
""""""""""
.. parameter:: details
   :type: object
   :collapse-children: false

   An object with payment details.

   .. parameter:: voucherNumber
      :type: string

      The voucher number, with the last four digits masked. When multiple gift cards are used, this is the first voucher
      number. Example: ``606436353088147****``.

   .. parameter:: giftcards
      :type: array

      A list of details of all giftcards that are used for this payment. Each object will contain the following
      properties.

      .. parameter:: issuer
         :type: string

         The ID of the gift card brand that was used during the payment.

      .. parameter:: amount
         :type: decimal

         The amount in EUR that was paid with this gift card.

      .. parameter:: voucherNumber
         :type: string

         The voucher number, with the last four digits masked. Example: ``606436353088147****``

   .. parameter:: remainderAmount
      :type: decimal

      Only available if another payment method was used to pay the remainder amount – The amount in EUR that was paid
      with another payment method for the remainder amount.

   .. parameter:: remainderMethod
      :type: string

      Only available if another payment method was used to pay the remainder amount – The payment method that was used
      to pay the remainder amount.

Giropay
"""""""
.. parameter:: details
   :type: object
   :collapse-children: false

   An object with payment details.

   .. parameter:: consumerName
      :type: string

      Generally only available one banking day after the payment has been completed – The consumer's name.

   .. parameter:: consumerAccount
      :type: string

      Generally only available one banking day after the payment has been completed – The consumer's bank account IBAN.

   .. parameter:: consumerBic
      :type: string

      Generally only available one banking day after the payment has been completed. The consumer's bank's BIC.

iDEAL
"""""
.. parameter:: details
   :type: object
   :collapse-children: false

   An object with payment details.

   .. parameter:: consumerName
      :type: string

      Only available if the payment has been completed – The consumer's name.

   .. parameter:: consumerAccount
      :type: string

      Only available if the payment has been completed – The consumer's IBAN.

   .. parameter:: consumerBic
      :type: string

      Only available if the payment has been completed – The consumer's bank's BIC.

KBC/CBC Payment Button
""""""""""""""""""""""
.. parameter:: details
   :type: object
   :collapse-children: false

   An object with payment details.

   .. parameter:: consumerName
      :type: string

      Only available one banking day after the payment has been completed – The consumer's name.

   .. parameter:: consumerAccount
      :type: string

      Only available one banking day after the payment has been completed – The consumer's IBAN.

   .. parameter:: consumerBic
      :type: string

      Only available one banking day after the payment has been completed – The consumer's bank's BIC.

PayPal
""""""
.. parameter:: details
   :type: object
   :collapse-children: false

   An object with payment details.

   .. parameter:: consumerName
      :type: string

      Only available if the payment has been completed – The consumer's first and last name.

   .. parameter:: consumerAccount
      :type: string

      Only available if the payment has been completed – The consumer's email address.

   .. parameter:: paypalReference
      :type: string

      PayPal's reference for the transaction, for instance ``9AL35361CF606152E``.

   .. parameter:: paypalPayerId
      :type: string

      ID for the consumer's PayPal account, for instance ``WDJJHEBZ4X2LY``.

   .. parameter:: paypalFee
      :type: decimal

      The amount of fee PayPal will charge for this transaction. This field is omitted if PayPal will not charge a fee
      for this transaction.

paysafecard
"""""""""""
.. parameter:: details
   :type: object
   :collapse-children: false

   An object with payment details.

   .. parameter:: consumerName
      :type: string

      The consumer identification supplied when the payment was created.

SEPA Direct Debit
"""""""""""""""""
.. parameter:: details
   :type: object
   :collapse-children: false

   An object with payment details.

   .. parameter:: transferReference
      :type: string

      Transfer reference used by Mollie to identify this payment.

   .. parameter:: creditorIdentifier
      :type: string

      The creditor identifier indicates who is authorized to execute the payment. In this case, it is a reference to
      Mollie.

   .. parameter:: consumerName
      :type: string

      The consumer's name.

   .. parameter:: consumerAccount
      :type: string

      The consumer's IBAN.

   .. parameter:: consumerBic
      :type: string

      The consumer's bank's BIC.

   .. parameter:: dueDate
      :type: date

      Estimated date the payment is debited from the consumer's bank account, in ``YYYY-MM-DD`` format.

   .. parameter:: signatureDate
      :type: date

      Only available if the payment has been verified – Date the payment has been signed by the consumer, in
      ``YYYY-MM-DD`` format.

   .. parameter:: bankReasonCode
      :type: string

      Only available if the payment has failed – The official reason why this payment has failed. A detailed description
      of each reason is available on the website of the European Payments Council.

   .. parameter:: bankReason
      :type: string

      Only available if the payment has failed – A textual desciption of the failure reason.

   .. parameter:: endToEndIdentifier
      :type: string

      Only available for batch transactions – The original end-to-end identifier that you've specified in your batch.

   .. parameter:: mandateReference
      :type: string

      Only available for batch transactions – The original mandate reference that you've specified in your batch.

   .. parameter:: batchReference
      :type: string

      Only available for batch transactions – The original batch reference that you've specified in your batch.

   .. parameter:: fileReference
      :type: string

      Only available for batch transactions – The original file reference that you've specified in your batch.

SOFORT Banking
""""""""""""""
.. parameter:: details
   :type: object
   :collapse-children: false

   An object with payment details.

   .. parameter:: consumerName
      :type: string

      Only available if the payment has been completed – The consumer's name.

   .. parameter:: consumerAccount
      :type: string

      Only available if the payment has been completed – The consumer's IBAN.

   .. parameter:: consumerBic
      :type: string

      Only available if the payment has been completed – The consumer's bank's BIC.

Vouchers
""""""""
.. parameter:: details
   :type: object
   :collapse-children: false

   An object with payment details.

   .. parameter:: issuer
      :type: string

      The ID of the voucher brand that was used during the payment. When multiple vouchers are used, this is the issuer
      of the first voucher.

   .. parameter:: vouchers
      :type: array

      A list of details of all vouchers that are used for this payment. Each object will contain the following
      properties.

      .. parameter:: issuer
         :type: string

         The ID of the voucher brand that was used during the payment.

      .. parameter:: issuerName
         :type: string

         The name of the voucher brand that was used during the payment.

      .. parameter:: amount
         :type: decimal

         The amount in EUR that was paid with this voucher.

   .. parameter:: remainderAmount
      :type: decimal

      Only available if another payment method was used to pay the remainder amount – The amount in EUR that was paid
      with another payment method for the remainder amount.

   .. parameter:: remainderMethod
      :type: string

      Only available if another payment method was used to pay the remainder amount – The payment method that was used
      to pay the remainder amount.

Mollie Connect response parameters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. parameter:: applicationFee
   :type: object
   :collapse: true

   The :doc:`application fee </connect/application-fees>`, if the payment was created with one.

   .. parameter:: amount
      :type: decimal

      The application fee amount in EUR as specified during payment creation.

   .. parameter:: description
      :type: string

      The description of the application fee as specified during payment creation.

QR codes (optional)
^^^^^^^^^^^^^^^^^^^
A QR code object with payment method-specific values is available for certain payment methods if you pass the include
``details.qrCode`` to the resource endpoint.

The ``qrCode`` key in the ``details`` object will then become available. The key will contain this object:

.. parameter:: height
   :type: integer

   Height of the image in pixels.

.. parameter:: width
   :type: integer

   Width of the image in pixels.

.. parameter:: src
   :type: string

   The URI you can use to display the QR code. Note that we can send both data URIs as well as links to HTTPS images.
   You should support both.

For an implemention guide, see our :doc:`QR codes guide </payments/qr-codes>`.

Example
-------

Request
^^^^^^^
.. code-block:: bash
   :linenos:

   curl -X GET https://api.mollie.com/v1/payments/tr_WDqYK6vllg \
       -H "Authorization: Bearer test_dHar4XY7LxsDOtmnkVtjNVWXLSlXsM"

Response
^^^^^^^^
.. code-block:: none
   :linenos:

   HTTP/1.1 200 OK
   Content-Type: application/json

   {
       "resource": "payment",
       "id": "tr_WDqYK6vllg",
       "mode": "test",
       "createdDatetime": "2018-03-16T14:30:07.0Z",
       "status": "paid",
       "paidDatetime": "2018-03-16T14:34:50.0Z",
       "amount": "35.07",
       "description": "Order 33",
       "method": "ideal",
       "metadata": {
           "order_id": "33"
       },
       "details": {
           "consumerName": "Hr E G H Kloppers en/of MW M.J. Kloppers-Veeneman",
           "consumerAccount": "NL53INGB0618365937",
           "consumerBic": "INGBNL2A"
       },
       "locale": "nl_NL",
       "profileId": "pfl_QkEhN94Ba",
       "links": {
           "webhookUrl": "https://webshop.example.org/payments/webhook",
           "redirectUrl": "https://webshop.example.org/order/33/"
       }
   }
