# @aqm/queuelogger

[![Travis CI][travis-image]][travis-url]
[![Greenkeeper badge](https://badges.greenkeeper.io/cfware/queuelogger.svg)](https://greenkeeper.io/)
[![NPM Version][npm-image]][npm-url]
[![NPM Downloads][downloads-image]][downloads-url]
[![MIT][license-image]](LICENSE)

CFWare AQM queue_log mysql writer

### Install @aqm/queuelogger

This module requires node.js 13.8.0 or above.

```sh
npm i --save @aqm/queuelogger
```

## Usage

```js
import {QueueLogger} from '@aqm/queuelogger';

class QueueManager {
	constructor() {
		this.logger = new QueueLogger({
			/* Default is 'P001', used as value of partition column. */
			partition: 'P001',
			/* Default is least significant part of hostname, used as value of serverid column. */
			serverID: 'serverid',
			/* Default: 'queue_log', the table we insert to. */
			tableName: 'queue_log',
			/* Override default settings used by require('mysql').createPool.
			 * The default database is queuemetrics.
			 */
			mysql: {},
		});

		process.on('SIGTERM', () => this.logger.end().catch(() => {}));
	}

	async writeQueueLog(timeID, callID, queue, agent, verb, data1, data2, data3, data4, data5) {
		/* This is a pointless example.  In real life a queue manager would retrieve some
		 * data from a channel or other object. */
		try {
			await this.logger.writeEntry(timeID, callID, queue, agent, verb, data1, data2, data3, data4, data5);
		} catch (err) {
			/* mysql write failed, record to a file. */
		}
	}
}
```

## Running tests

Tests require access to a test mysql or mariadb daemon.  The test database server must
have a `queuemetrics` database and a `queuelogd` user as defined by `sampledb/qm.sql`.

Default test configuration:
```sh
npm config set @aqm/queuelogger:dbhost 'localhost'
npm config set @aqm/queuelogger:dbprivuser 'root'
npm config set @aqm/queuelogger:dbprivpassword ''
```

These settings can be changed to use a different server or to use an account less
privileged than root.  The `priv` account must have SELECT and DELETE access to
the `queuemetrics.queue_log` table.

Once the database is created and a privileged account configured the tests can be run:
```sh
npm install
npm test
```

[npm-image]: https://img.shields.io/npm/v/@aqm/queuelogger.svg
[npm-url]: https://npmjs.org/package/@aqm/queuelogger
[travis-image]: https://travis-ci.org/cfware/queuelogger.svg?branch=master
[travis-url]: https://travis-ci.org/cfware/queuelogger
[downloads-image]: https://img.shields.io/npm/dm/@aqm/queuelogger.svg
[downloads-url]: https://npmjs.org/package/@aqm/queuelogger
[license-image]: https://img.shields.io/github/license/cfware/queuelogger.svg
