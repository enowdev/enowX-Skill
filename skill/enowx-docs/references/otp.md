# OTP (disposable SMS via Warpize)

Rent throwaway phone numbers to receive OTP/verification codes. enowX proxies to
**Warpize**; you bring your own Warpize key (`wz_live_…`) — get one at
https://warpize.com. The key is stored encrypted; enowX never sees the codes.

## Flow

1. `POST /api/otp/config` `{ api_key }` — save your `wz_live_…` key.
   (`GET /api/otp/config` shows whether a key is set + a masked preview;
   `DELETE /api/otp/config` removes it.)
2. `GET /api/otp/balance` — your Warpize balance.
3. `GET /api/otp/services?q=` — available services + countries + prices.
4. `POST /api/otp/rent` `{ service, country }` — rent a number; returns an order.
5. `GET /api/otp/orders/{id}` — poll the order until the SMS code arrives.
6. `POST /api/otp/orders/{id}/cancel` — cancel an order.

Fetch `/api/docs` (group "OTP") for the exact params of the running version.
