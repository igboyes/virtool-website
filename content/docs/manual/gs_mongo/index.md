---
title: "MongoDB"
description: "Install and upgrade MongoDB."
menu:
  manual:
    parent: "Getting Started"
    weight: 30
---

# Install MongoDB

Virtool uses MongoDB v3.6.0+ as a database service. You will have to get MongoDB running before starting Virtool. We highly recommend installing and updating MongoDB through your Linux package manager.

The MongoDB documentation provides step-by-step instructions for installing MongoDB on common Linux distributions:

- [Install on Ubuntu](https://docs.mongodb.com/v3.6/tutorial/install-mongodb-on-ubuntu)
- [Install on Debian](https://docs.mongodb.com/v3.6/manual/tutorial/install-mongodb-on-debian)
- [Install on SUSE](https://docs.mongodb.com/v3.6/tutorial/install-mongodb-on-suse)
- [Install on Red Hat](https://docs.mongodb.com/v3.6/tutorial/install-mongodb-on-red-hat)

Once you have installed MongoDB, ensure it is running by issuing the following command:

```shell
sudo systemctl status mongod.service
```

You will receive an output similar to the following if MongoDB is running:

```shell
● mongod.service - High-performance, schema-free document-oriented database
   Loaded: loaded (/etc/systemd/system/mongod.service; disabled; vendor preset: enabled)
   Active: active (running) since Fri 2017-04-21 15:55:59 PDT; 2s ago
 Main PID: 11844 (mongod)
    Tasks: 14
   Memory: 31.1M
      CPU: 95ms
   CGroup: /system.slice/mongod.service
           └─11844 /usr/bin/mongod --quiet --config /etc/mongod.conf
```

# Upgrade MongoDB

In future versions of Virtool, an upgrade to MongoDB may be required for continued compatibility. This is a simple process for users experienced with command line usage and basic administration.

## Upgrade MongoDB 3.4 to 3.6 {#mongo_3_6_0}

{{< note color="red" >}}
**MongoDB 3.6.0 or newer will be required in Virtool 4.0.0**.
- Virtool 4.0.0 will not start if it detects that the configured database does not meet this requirement
- Virtool versions prior to 4.0.0 are not compatible with MongoDB 4.0.0
{{</ note >}}

### 1. Backup the Database

First, backup your database using the following command (assuming your database is called `virtool`). This will dumps the entire database to a folder that can later be restored if necessary.

```shell
mongodump -d virtool
```

### 2. Update Compatibility Version

Next, it is necessary to [update a MongoDB administrative value called `featureCompatibilityVersion`](https://docs.mongodb.com/manual/release-notes/3.6-upgrade-standalone/#prerequisites). Open a Mongo shell:

```shell
mongo
```

In the shell, check the current `featureCompatibilityVersion` value:

```js
db.adminCommand({ getParameter: 1, featureCompatibilityVersion: 1 })
```

The response should contain the current version compatibility information:
```js
// Nothing needs to be done in this case.
{ "featureCompatibilityVersion" : { "version" : "3.4" }, "ok" : 1 }

// Feature compatibility must be set to 3.4 in this case.
{ "featureCompatibilityVersion" : { "version" : "3.2" }, "ok" : 1 }
```

**If the `version` value is not `3.4`**, the compatibility version need to be updated:
```js
db.adminCommand({ setFeatureCompatibilityVersion: "3.4" })
```

The `featureCompatibility` value should now be set to `3.4`. Check using:
```js
db.adminCommand({ getParameter: 1, featureCompatibilityVersion: 1 })
```

The response should have the value set to `3.4`.
```js
{ "featureCompatibilityVersion" : { "version" : "3.4" }, "ok" : 1 }
```

### 3. Update Database Software

Install MongoDB 3.6 according to the instructions from MongoDB:

- [Install on Ubuntu](https://docs.mongodb.com/v3.6/tutorial/install-mongodb-on-ubuntu)
- [Install on Debian](https://docs.mongodb.com/v3.6/tutorial/install-mongodb-on-debian)
- [Install on SUSE](https://docs.mongodb.com/v3.6/tutorial/install-mongodb-on-suse)
- [Install on Red Hat](https://docs.mongodb.com/v3.6/tutorial/install-mongodb-on-red-hat)


### 4. Update Compatibility Version

The `featureCompatibilityVersion` now needs to be set to `3.6`. This process is the same as in **step 2** except the value is being set to `3.6` instead of `3.4`.

Open the Mongo shell and set the compatibility version:
```js
db.adminCommand({ setFeatureCompatibilityVersion: "3.6" })
```

The `featureCompatibility` value should now be set to `3.6`. Check using:
```js
db.adminCommand({ getParameter: 1, featureCompatibilityVersion: 1 })
```

The response should show the `version` value set to `3.6`.
```js
{ "featureCompatibilityVersion" : { "version" : "3.6" }, "ok" : 1 }
```

### 5. Continue Upgrading

We suggest continuing into the next section and [upgrading MongoDB to 4.2](#upgrade-mongodb-to-42).

{{< note >}}

**The MongoDB documentation provides instructions for [upgrading standalone MongoDB to 3.6](https://docs.mongodb.com/manual/release-notes/3.6-upgrade-standalone/).**

{{</ note >}}

## Upgrade MongoDB to 4.2

You must successively upgrade major releases until you reach MongoDB 4.2.

This will involve repeating the [previous section's steps](#mongo_3_6_0). **You only need to backup the database once, before beginning the successive update process.

The required updates to reach 4.2 from 3.6 are:

- 3.6 {{< icon "fas fa-long-arrow-alt-right" >}} 4.0 ([MongoDB Docs](https://docs.mongodb.com/manual/release-notes/4.0-upgrade-standalone/))
- 4.0 {{< icon "fas fa-long-arrow-alt-right" >}} 4.2 ([MongoDB Docs](https://docs.mongodb.com/manual/release-notes/4.2-upgrade-standalone/))

For each upgrade you must:

1. Ensure `featureCompatibilityVersion` is set correctly before software upgrade
2. Upgrade MongoDB software
3. Set `featureCompatibilityVersion` to match the version of MongoDB you installed