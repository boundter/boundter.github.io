---
layout: post
title: "nonlinear_systems"
date: 2018-03-02
---

During research I spend a lot of time in numerically exploring systems.
Sometimes the same system, but with different parameters and sometimes different
systems. Building such a simulation always follows the same general steps:

1. Define the ordinary differential equation (ODE)
2. Setup the integrator
3. Set the initial conditions
4. Integrate, and after every step save the quantities you are interested in,
   while making sure you do not change the current state

Doing these things again and again becomes very tedious. There are some
shortcuts, for example the integrator is usually the same Runge-Kutta 4th order
algorithm and only needs to be included, but in general there is a lot of
repetitive code. Aside from the repetitiveness I found there to be two big
caveats in this style of work.

First of all there is a disconnect between the relations you want to
research and the coding. While developing the simulation a lot of care has to be
taken, not to change the current state or time, to use the correct state for
integration and how to calculate the quantities you are interested in. It
becomes more about the how, than the what. Ideally you could think about the
high level abstraction, like calculating the mean field, instead of how to
calculate the mean field. Writing the simulation is imperative, while a
physicist thinks declarative about the topic.

The second caveat are the bugs. When writing a lot of repetitive code, without
unit tests. There are bound to be some bugs. To combat this one usually tests
the simulation with a known system or a known result. If it does not fit there
is some bug that needs to be solved. This becomes problematic, when you consider
problems that are not easily tested, "Is this weird result I just got a
phenomenon worth looking at, or was parameter X passed wrongly?".

These are problems I tried to solve with nonlinear_systems (the name is way too
long, it could probably be shortened to nl, libnl or such). In its core
nonlinear_sytems (nl for short from now on) is a wrapper around the boost
package odeint. It allows a simple definition of ODEs and a somewhat declarative
integration. The name of nl is a bit counter intuitive. It can also be used for
linear ODEs, but since they can be seen as a subset of nonlinear ODES and I
mostly work with such a type of ODE, this may hopefully be excused.

Lets begin with the second point. All methods of nl are unit tested, to makre
sure no bugs are wriggling into your work. For most parameters there is a sanity
check, like checking the size of two array, to see if they fit together. This
allows the user to have some confidence in their results.

The general idea behind nl is to use object-oriented programming to solve the
ODEs.
