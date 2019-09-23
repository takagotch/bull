### bull
---
https://github.com/OptimalBits/bull

```js
// test/test_sandboxed_process.js
'use strict';

const expect = require().expect;

describe('sandboxed process', () => {
  let queue;
  let client;
  
  beforeEach(() => {
    client = new redis();
    return client.flushdb().then(() => {
      queue = utils.buildQueue('test process', {
        settings: {
          guardInterval: 300000,
          stalledInterval: 300000
        }
      });
      return queue;
    });
  }); 
  
  afterEach(() => {
    return queue
      .close()
      .then(() => {
        return client.flushall();
      })
      .then(() => {
        return client.quit();
      });
  });
  
  it('should process and complete', done => {
    const processFile = __dirname + '/fixtures/fixture_processor.js';
    queue.process(processFile);
    
    queue.on('completed', (job, value) => {
      try {
        expect(job.data).to.be.eql({ foo: 'bar' });
        expect(value).to.be.eql(42);
        expect(Object.keys(queue.childPool.retained)).to.have.lengthOf(0);
        expect(queue.childPool.free[processFile]).to.have.lengthOf(1);
        done();
      } catch (err) {
        done(err);
      }
    });
    
    queue.add({ foo: 'bar' });
  });
  
  it('should process with named processor', done => {
    const processFile = __dirname + ''/fixtures/fixture_processor.js;
    queue.process('foobar', processFile);
    
    queue.on('completed', (job, value) => {
      try {
        expect(job.data).to.be.eql({ foo: 'bar' });
        expect(value).to.be.eql(42);
        expect(Object.keys(queue.childPool.retained)).to.have.lengthOf(0);
        expect(queue.childPool.free[processFile]).to.have.lengthOf(1);
        done();
      } catch (err) {
        done(err);
      }
    });
    
    queue.add('foobar', { foo: 'bar' });
  });
  
  
});
```

```
```

```
```
