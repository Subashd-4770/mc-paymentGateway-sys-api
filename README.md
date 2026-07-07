Flow Functionality:

Main API Flow:

Receives all incoming API requests through the HTTP Listener.
Routes requests to the appropriate APIKit flow based on the RAML specification.
Returns standardized API responses and handles APIKit validation errors such as invalid requests, unsupported methods, and missing resources.

Create Customer Flow:

Accepts customer details from the request.
Transforms the payload using DataWeave.
Creates a new customer in Stripe using the Stripe Connector.
Logs the request and response for monitoring.

List Products Flow:

Retrieves all available products from Stripe.
Transforms the response into the required format.
Logs the retrieved product information.

Delete Customer Flow:

Deletes a customer from Stripe using the provided customer ID.
Logs the start and completion of the delete operation.

Payment Processing Flow:

Receives payment details including amount, currency, and payment method.
Transforms the request into the format required by the Stripe Payment Intent API.
Sets the required authorization headers using the configured Stripe Secret Key.
Invokes the Stripe Payment Intent API using an HTTP Request with a 5-second timeout.
Uses the Until Successful scope to automatically retry transient failures up to 5 times with a 6-second retry interval.
Maps Stripe responses into a simplified business response containing payment status, payment ID, amount, currency, and timestamp.
Logs all payment requests, successful responses, and failures for auditing and troubleshooting.

Error Handling:

The application implements centralized error handling at both the API level and the payment flow level to provide meaningful responses and improve reliability.

APIKit Error Handling:

Handles request validation and routing errors before business processing begins.

Supported errors include:

400 Bad Request – Invalid request payload or parameters.
404 Not Found – Requested resource does not exist.
405 Method Not Allowed – Unsupported HTTP method.
406 Not Acceptable – Unsupported response format.
415 Unsupported Media Type – Invalid Content-Type.
501 Not Implemented – Requested operation is not implemented.

Each error returns a standardized JSON response with the appropriate HTTP status code.

Payment Flow Error Handling:

The payment flow includes processor-level error mapping and flow-level error handling to manage communication with Stripe.

Timeout Handling:

Detects when Stripe does not respond within the configured timeout.
Returns 504 Gateway Timeout with a user-friendly message.

Connectivity Handling:

Handles network failures or unavailable Stripe services.
Returns 503 Service Unavailable.

Retry Exhausted:

Triggered when all retry attempts configured in the Until Successful scope fail.
Returns 503 Service Unavailable indicating the payment could not be completed after multiple attempts.

Payment Processing Failure:

Handles unexpected errors returned during payment processing.
Returns 500 Internal Server Error with a business-friendly response.

Generic Exception Handling:

Catches any unhandled exceptions not covered by specific handlers.
Returns a standardized 500 Internal Server Error response to ensure consistent API behavior.
