# Script Item Booking

## Purpose

Monitor a single POP MART Singapore product page from a local Chrome session. The script can log when the storefront exposes a purchase button and optionally advance to checkout.

## Aims

- Reduce repetitive manual stock checks for a selected product.
- Keep the target URL, polling interval, and checkout behavior configurable.
- Use an existing Chrome profile so the monitor can work with the user's storefront session.

## Project contents

| Path | Role |
| --- | --- |
| [`main.py`](main.py) | Connects to Chrome and runs the asynchronous monitoring loop. |
| [`config.py`](config.py) | Sets the product URL, Chrome debugging endpoint, timing, and action flags. |
| [`browser/manager.py`](browser/manager.py) | Connects through the Chrome DevTools Protocol and finds or opens the product tab. |
| [`monitor/stock.py`](monitor/stock.py) | Checks whether the page has a visible, enabled `BUY NOW` button. |
| [`purchase/actions.py`](purchase/actions.py) | Handles the optional checkout navigation. |
| [`utils/logger.py`](utils/logger.py) | Prints time-stamped messages to the terminal. |

## Implementation

The Python script uses Playwright to attach to Chrome over CDP. It refreshes the product page, waits for JavaScript to render, and checks the button by its accessible role and name. Each check uses the button state as a proxy for availability. When `AUTO_BUY_NOW` is enabled, the script clicks **Buy Now**, checks for checkout in the current or a new tab, and looks for **Check Now** or **Proceed to Pay**.

### Run in monitoring mode

1. Install Playwright into a Python environment with `python3 -m pip install playwright`.
2. Launch Google Chrome with a separate browser profile and remote debugging on port 9222. See [`initialize.txt`](initialize.txt) for the macOS launch command.
3. Set `PRODUCT_URL` in [`config.py`](config.py). Set both `AUTO_BUY_NOW` and `AUTO_ADD_TO_BAG` to `False` for monitoring only. The checked-in configuration enables `AUTO_BUY_NOW`.
4. From the repository root, run `python3 main.py`. Press Ctrl+C to stop.

## Results

The monitor prints numbered checks and reports **SOLD OUT** when it finds no usable button or **PRODUCT MAY BE AVAILABLE** when it finds one. Stock-check exceptions also return the sold-out status. The checkout path stops monitoring after the checkout control is clicked. Order confirmation and payment submission are outside the script's scope. Keep `AUTO_ADD_TO_BAG` disabled because the corresponding method is unimplemented.

## Conclusion

This prototype makes product-page checks repeatable. Its stock signal depends on the storefront's button labels and behavior, so confirm availability and checkout status in Chrome.
