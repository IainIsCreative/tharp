# tharp

![Dancing Robots](https://raw.githubusercontent.com/dtex/tharp/master/assets/tharp-banner-color.png)

[![Join the chat at https://gitter.im/dtex/tharp](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/dtex/tharp?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

[![Build Status](https://travis-ci.org/dtex/tharp.svg)](https://travis-ci.org/dtex/tharp)

Tharp is an inverse kinematics solver and robot manager designed to work with Johnny-Five. It makes the hard things easy. Check out this [video](http://www.youtube.com/watch?v=gabm8QdO29Q) highlighting Tharp's  performance.


### Installation
````bash
npm install tharp
````

### Documentation
Tharp documentation can be found in the [wiki](https://github.com/dtex/tharp/wiki).

### Example
````js
// This is a simple excerpt, illustrating the configuration
// of robot with two legs
var five = require("johnny-five"),
  tharp = require("tharp");

var board = new five.Board().on("ready", function() {

  /* === Begin robot configuration === */

  // Tharp.Chain() wraps our actuators and adds properties
  // that define the kinematic system. The "chainType" property defines
  // the joint/link configuration.
  var leftLeg = new tharp.Chain({
    constructor: five.Servos,
    chainType: "ZYY",
    origin: [4.25, 0.15, 2.875],
    links: [ 2.6, 7.6125, 10.4],
    actuators: [
      {pin:40, offset: 24, startAt: 0, range: [0, 90] },
      {pin:39, offset: 87, startAt: 78, range: [-80, 78] },
      {pin:38, offset: 165, invert: true, startAt: -140, range: [-160, -10] }
    ]
  });

  var rightLeg = new tharp.Chain({
    constructor: five.Servos,
    chainType: "ZYY",
    origin: [-4.25, 0.15, 2.875],
    links: [ 2.6, 7.6125, 10.4],
    actuators: [
      {pin:27, offset: -31, startAt: 180, range: [90, 180] },
      {pin:26, offset: -77, startAt: 102, range: [110, 260] },
      {pin:25, offset: -176, invert: true, startAt: 320, range: [180, 340]}
    ]
  });

  // Robot() wraps our chains, gives us a place to orient the
  // robot chassis and ensures that all chains can be rendered
  // before rendering the entire robot movement
  var robot = new tharp.Robot({
    chains: [rightLeg, leftLeg]
  });

  /* === End robot configuration === */

  // Now we can orient the chassis
  robot.orientation({ pitch: 0.1, roll: -0.08, yaw: -0.13 });
  robot.offset([ 1, -1, -0.2 ]);

  // And position our end effectors
  rightLegChain.solve({position: [8.25, 2.5, -5.0]});
  leftLegChain.solve({position: [-8.25, -2.25, -5.0]});

  // Move everything
  robot.render();

});

````
### Breaking Changes
* 1.0.0 Swapped Y and Z axes to better match best practices. Renamed "CoxaY-FemurZ-TibiaZ" chain type to "ZYY".
* 2.0.0 Changes ```segments``` to an array since part names are arbitrary. One person's "coxa" is another person's "hip" and then another person's "shoulder".
* 3.0.0 Changes ```segments``` to ```links``` to reflect the proper name.
