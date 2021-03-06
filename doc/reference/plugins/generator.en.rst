.. _generator-plugin:

Generator Plugin
****************

.. Licensed to the Apache Software Foundation (ASF) under one
   or more contributor license agreements.  See the NOTICE file
  distributed with this work for additional information
  regarding copyright ownership.  The ASF licenses this file
  to you under the Apache License, Version 2.0 (the
  "License"); you may not use this file except in compliance
  with the License.  You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing,
  software distributed under the License is distributed on an
  "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
  KIND, either express or implied.  See the License for the
  specific language governing permissions and limitations
  under the License.

The `Generator` allows testing of synthetic workloads by generating
HTTP responses of various sizes. The size and cacheability of the
response is specified by the first two coomponents of the requested
URL path. This plugin only supports the ``GET`` and ``HEAD`` HTTP
methods.

+---------------+----------------------------------------------------------------+
|Path component | Description                                                    |
+===============+================================================================+
|1              | ``cache`` or ``nocache``. If ``cache`` is specifed, the        |
|               | `Generator` plugin will respond with ``Cache-Control`` headers |
|               | marking the response as cacheable for 24 hours.                |
|2              | Integral number of bytes to return in the response.            |
+---------------+----------------------------------------------------------------+

Path components after the first 2 are ignored. This means that the
trailing path components can be manipulated to create unique URLs
following any covenient convention.

The `Generator` plugin publishes the following metrics:

  generator.response_bytes:
    The total number of bytes emitted
  generator.response_count:
    The number of HTTP responses generated by the plugin

Examples:
---------

The most common way to use the `Generator` plugin is to configure
it as a remap plugin in :file:`remap.config`::

  map http://workload.example.com/ http://127.0.0.1/ \
    @plugin=generator.so

Notice that although the remap target is never contacted because
the `Generator` plugin intercepts the request and acts as the origin
server, it must be syntactically valid and resolvable in DNS.

A 10 byte, cacheable object can then be generated::

  $ curl -o /dev/null -x 127.0.0.1:8080 http://workload.example.com/cache/10/caf1fc92332b3a3c8cb8b3826b6a1658

The `Generator` plugin can return responses as large as you like::

  $ curl -o /dev/null -x 127.0.0.1:8080 http://workload.example.com/cache/$((10 * 1024 * 1024))/$RANDOM

