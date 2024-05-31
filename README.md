ESP-NOW component docs
======================

Find the low-level ESP-NOW documentation [here](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/network/esp_now.html).

Find the higher-level component documentation here:

* [Core ESPNOW API](https://george-hawkins.github.io/esp-now-docs-v2024-05-31/api-reference/espnow/espnow.html).
* [Supporting APIs (Control, Debug, OTA, Provisioning, Security and Utils)](https://george-hawkins.github.io/esp-now-docs-v2024-05-31/api-reference/index.html).

Add the component to an existing ESP-IDF project with:

```
$ idf.py add-dependency "espressif/esp-now=*"
```

The user guide for the component is [here](https://github.com/espressif/esp-now/blob/master/User_Guide.md).

But a better guide to how to use the APIs are the examples found [here](https://github.com/espressif/esp-now/tree/master/examples).

To create a new project that's set up to include the `espressif/esp-now` component and is derived from one of the examples, do (as described [here](https://github.com/espressif/esp-now/blob/master/README.md#example)):

```
idf.py create-project-from-example "espressif/esp-now=*:coin_cell_demo/bulb"
```

Details
-------

The ESP-IDF includes a basic interface to ESP-NOW that's documented [here](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/network/esp_now.html).

However, Espressif also provide a component of the same name that can be found [here](https://github.com/espressif/esp-now/).

To quote the component's [user guide](https://github.com/espressif/esp-now/blob/master/User_Guide.md):

> this component provides some high-level functionalities to simplify the use of ESP-NOW protocol, you can understand this component as an application-level ESP-NOW, which provides some enhanced features, including pairing, control, provisioning, debug, OTA, security, etc.

You can recognize code that uses this component by the fact that it includes the header `espnow.h` rather than `esp_now.h`.

This distinction seems to cause a lot of confusion when people find ESP-NOW examples on the web - they try out the example and the compile step fails as `espnow.h` isn't available by default.

In order to resolve this, one has to add the ESP-NOW component to the current project (as noted in the component's [`README`](https://github.com/espressif/esp-now/blob/master/README.md)):

```
$ idf.py add-dependency "espressif/esp-now=*"
```

As well as the [user guide](https://github.com/espressif/esp-now/blob/master/User_Guide.md), the component's repo contains a [`docs` folder](https://github.com/espressif/esp-now/tree/master/docs).

This contains the source for the documentation and it has to be compiled up using [`esp-docs`](https://github.com/espressif/esp-docs).

The folder's `README` claims that compiled versions are available online but at the time of writing (May 31st, 2024), the two links given just result in 404 errors.

I've compiled up the current (as of May 31st, 2024) docs and made them available [here](https://george-hawkins.github.io/esp-now-docs-v2024-05-31/api-reference/espnow/espnow.html).

They were compiled on an Ubuntu system like so:

```
$ git clone https://github.com/espressif/esp-now.git
$ cd esp-now/docs
$ git tag -a v2024-05-31 -m 'Version 2024-05-31'
$ sudo apt install doxygen
$ python3 -m venv venv
$ source venv/bin/activate
$ pip install --upgrade pip
$ build-docs -t esp32 -l en
$ ls _build/en/esp32/html
api-reference/  genindex.html  index.html  objects.inv  search.html  searchindex.js  see  _sources/  _static/
```

The process depends on `doxygen` being available, hence the `apt install doxygen` step, and it only produces documentation for the latest tag so, you have to create a tag corresponding to the latest state of the repo to get the latest version.

You need to create an annotated tag (with `-a`) otherwise things may seem to work today but mysteriously fail later with an error like this:

```
fatal: no tag exactly matches 'a481b291fd420d77222c74c4a0396e07888cfec1'
Git commit ID:  a481b29
```

If you see this error, it's actually coming from these lines:

```
html_context = {
    'display_github': True,  # Add 'Edit on Github' link instead of 'View page source'
    'github_version': get_github_rev(),
}
```

They're in the file `site-packages/esp_docs/conf_docs.py` found deep in your `venv` directory. The `get_github_rev()` call finds the latest tag but if it's a lightweight non-annotated tag a fatal error may result (or may not depending on the phase of the moon).

But the solution is easy enough - as seen above, just create an annotated tag.
