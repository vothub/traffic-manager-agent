# Traffic Manager Agent

Middleware in your Node application to collect the incoming requests.

## Integrating into Node app

### First steps

Add agent module to your project's dependencies

```
npm install @vot/traffic-manager-agent --save
```

After this just follow the instructions to generate accesslog of your application
(Apache Common Log Format), send data to Traffic Manager Hub or both.


### Basic accesslog

This is enough to get a full access log generated in your application.

Produces Apache Common Log Format (CLF) which can be analysed further with
tools readily available.


Just register the middleware before other routes in your app and restart the app.

```
const TrafficManagerAgent = require('@vot/traffic-manager-agent');

const trafficManagerOpts = {
  reporters: {
    File: {
      enabled: true,
      format: 'apache'
      location: './access_log'
    },
  }
};

const trafficManagerMiddleware = TrafficManagerAgent(trafficManagerOpts);

app.use(trafficManagerMiddleware);
```


### Sending data to Traffic Manager Hub

You can use this module to integrate with Traffic Manager Hub directly.

This is desired when you need more insight into your application's incoming traffic,
understand the patterns in it, perform early threat analysis and shape the traffic
with customisable rate limiting.

```
const TrafficManagerAgent = require('@vot/traffic-manager-agent');

const trafficManagerOpts = {
  reporters: {
    TMHub: {
      enabled: true,
      rate: 10,
      url: 'https://trafficmanager.example.com',
      siteId: '2fb5193d923f4b71ad6a771e8700aa1f',
      siteSecret: 'zN2EY69fmy303MUEs7ZkQv8sqeuFjFEp'
    }
  }
};

const trafficManagerMiddleware = TrafficManagerAgent(trafficManagerOpts);

app.use(trafficManagerMiddleware);
```

### Available reporters

You can combine multiple reporters in a single application.

For example you can have accesslog produced for the teams maintaining infrastructure,
standard output to console for developers and a web UI with basic analysis for
everyone else.

Simply expand the reporters property by providing more outputs.

Each reporter has at least `enabled` property which accepts `true` and `false` values.

**Available reporters:**

- StdOut
- File
- TMHub


```
const TrafficManagerAgent = require('@vot/traffic-manager-agent');

const trafficManagerOpts = {
  reporters: {
    File: {
      enabled: true,
      format: 'apache'
      location: './access_log'
    },
    StdOut: {
      enabled: true
    },
    TMHub: {
      enabled: true,
      rate: 30,
      url: 'https://trafficmanager.example.com',
      siteId: '2fb5193d923f4b71ad6a771e8700aa1f',
      siteSecret: 'zN2EY69fmy303MUEs7ZkQv8sqeuFjFEp'
    }
  }
};

const trafficManagerMiddleware = TrafficManagerAgent(trafficManagerOpts);

app.use(trafficManagerMiddleware);
```
