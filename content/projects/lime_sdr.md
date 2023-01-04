+++
title = "Lime SDR"
author = ["Tze-Yang Tung"]
date = 2023-01-04
lastmod = 2023-01-04T10:47:04+00:00
tags = ["Projects"]
draft = false
weight = 2001
+++

## Move away from GNURadio {#lime_sdr_a}

GNURadio is too hard to maintain.
It has too many dependencies and is hard to write modules for.
It would be better to simplify to just a Python script with SoapySDR interfaces.


## Rust implementation {#lime_sdr_b}

The SoapySDR C++ library has Rust bindings, which can be used to set the LimeSDR parameters.
Rust should provide much better efficiency compared to Python, although Python might be easier to for experimentation.
A small proof-of-concept project that uses the Rust bindings to send a singletone sinusoid is [completed](https://github.com/r0ymanesco/rust-singletone).
