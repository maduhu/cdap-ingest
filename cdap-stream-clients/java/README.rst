.. meta::
    :author: Cask Data, Inc.
    :copyright: Copyright © 2014-2016 Cask Data, Inc.
    :license: See LICENSE file in this repository

===========================
CDAP Stream Client for Java
===========================

The Stream Client Java API is for managing Streams from Java applications.

Supported Actions
=================

- Create a Stream
- Update TTL (time-to-live, in seconds) for an existing Stream
- Retrieve the current Stream TTL (in seconds)
- Truncate an existing Stream (the deletion of all events that were written to the Stream)
- Write an event to an existing Stream


Build
=====
To build the Stream Client Java API jar, use::

    mvn clean package


Usage
=====
To use the Stream Client Java API, include this Maven dependency in your project's
``pom.xml`` file::

    <dependency>
      <groupId>co.cask.cdap</groupId>
      <artifactId>cdap-stream-client</artifactId>
      <version>{cdap-stream-client-version}</version>
    </dependency>


Examples
========

Creating a StreamClient
-----------------------
Create a StreamClient instance, specifying the fields ``host`` and ``port`` of the CDAP instance::

    StreamClient streamClient = new RestStreamClient.Builder("localhost", 11015).build();

Optional configurations that can be set (and their default values):

- ``ssl``: ``false`` (set to ``true`` to use HTTPS protocol)
- ``verifySSLCert``: ``true`` (set to ``false`` to suspend certificate checks; this allows self-signed
  certificates to be used when SSL is ``true``)
- ``authClient``: ``null`` (`CDAP Authentication Client
  <https://github.com/caskdata/cdap-clients/tree/develop/cdap-authentication-clients/java>`__
  to interact with a secure CDAP instance)

Example::

    StreamClient streamClient = new RestStreamClient.Builder("localhost", 11015)
                                                    .ssl(true)
                                                    .authClient(authenticationClient)
                                                    .build();

Creating a Stream
-----------------
Create a new Stream with the ``<stream-id>`` *streamName*::

    streamClient.create("streamName");

**Notes:**

- The ``<stream-id>`` should only contain ASCII letters, digits and hyphens.
- If the Stream already exists, no error is returned, and the existing Stream remains in place.

Creating a StreamWriter
-----------------------
Create a ``StreamWriter`` instance for writing events to the Stream *streamName*::

    StreamWriter streamWriter = streamClient.createWriter("streamName");

Writing Stream Events
---------------------
To write new events to the Stream, use the ``StreamWriter`` interface::

    ListenableFuture<Void> future = streamWriter.write("New log event", Charsets.UTF_8);

Truncating a Stream
-------------------
To delete all events that were written to the Stream *streamName*, use::

    streamClient.truncate("streamName");

Update the Stream Time-to-Live (TTL)
------------------------------------
Update TTL (in seconds) for the Stream *streamName*::

    streamClient.setTTL("streamName", newTTL);

Get the Stream Time-to-Live (TTL)
---------------------------------
Get the current TTL value (in seconds) for the Stream *streamName*::

    long ttl = streamClient.getTTL("streamName");

Close the Clients
-----------------
When you are finished, release all resources by calling these two methods::

     streamWriter.close();
     streamClient.close();
