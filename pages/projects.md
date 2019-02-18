---
layout: page
title: Projects
permalink: /projects
---

## nonlinear_systems (Version 0.1)

In its core nonlinear_systems is a C++ wrapper around the odeint package for
integration for differential equations. This allows a quick development and a
seperates high level abstraction, like getting the mean field, from low level
implementation.

As an added bonus there is a wrapper around the program_options lib to allow a
quick creation of command line tools. All code is unit-tested for the intended
cases and can be installed with cmake.

#### Used Tools
- C++
- CMake for building, installing and testing
- Doxygen for documentation
- Boost
  - odeint for integration of the ODEs
  - program_options for adding command line arguments
  - test for executing the unit-tests

#### Example
```c++
#include <iostream>
#include <vector>
#include <nonlinear_systems/systems/generic_system.hpp>
#include <nonlinear_systems/observers/position_observer.hpp>

namespace nl = nonlinear_systems;

typedef  std::vector<double> state_type;

class HarmonicOscillatorODE {
  public:
    double _omega;

    HarmonicOscillatorODE(void* params) {
      _omega = reinterpret_cast<double*>(params)[0];
    }

    void operator()(const state_type& x, state_type& dx, double t) {
      dx[0] = x[1];
      dx[1] = -_omega*_omega*x[0];
    }
};

int main() {
  double omega[] = {1.};
  auto HarmonicOsc = nl::GenericSystem<HarmonicOscillatorODE>(1, 2, omega);
  state_type initial = {0., 1.};
  HarmonicOsc.SetPosition(initial);
  std::vector<state_type> x; std::vector<double> t;
  double dt = 0.1; int n_steps = 10;
  HarmonicOsc.Integrate(dt, n_steps, nl::PositionObserver<state_type>(x, t));
  for (size_t i = 0; i < x.size(); ++i) {
    std::cout << t[i] << ": (" <<
      x[i][0] << ", " << x[i][1] << ")" << std::endl;
  }
}
```

The output of this program is

```
0: (0, 1)
0.1: (0.0998333, 0.995004)
0.2: (0.198669, 0.980067)
0.3: (0.29552, 0.955337)
0.4: (0.389418, 0.921061)
0.5: (0.479425, 0.877583)
0.6: (0.564642, 0.825336)
0.7: (0.644217, 0.764843)
0.8: (0.717356, 0.696707)
0.9: (0.783326, 0.621611)
1: (0.84147, 0.540303)
```

## T8 (Final Version)

This is a rework for the introdcutory lab of the University of Potsdam. It
consists of the manual in LaTeX including some figures in Python and a
simulation in JavaScript. A given number of particels under the influence of
Brownian motion are animated and their displacement is compared to a normal
distribution.

#### Used Tools
- LaTeX
- Python
  - numpy to generate random numbers
  - matplotlib for the plotting of the figures in the manual
- JavaScript
  - Charts.js for the dynamic plotting in the simulation

![T8 Screenshot](../images/T8_screenshot.png)

## DAAFi
