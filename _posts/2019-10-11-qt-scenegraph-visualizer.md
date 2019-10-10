---
theme: jekyll-theme-hacker
layout: default
title: Qt Scenegraph Visualizer
date: 2019-10-11  00:00:00
---


# Qt Scenegraph Visualizer

A feature of Qt/QML that I have always liked but never can find ( is it a viewer, a display etc.) is the [Scenegraph Visualizer](https://doc.qt.io/qt-5/qtquick-visualcanvas-scenegraph-renderer.html#visualizing). This allows the GUI components used to be viewed in different ways to expose errors and anomalies not apparent when displayed in the final form.

The visualization is controlled by the QSG_VISUALIZE environmental variable that can get set to one of three values.

For linux this is relatively easy to apply from the command line by just exporting the variable prior to running the application.

For MacOS the situation is less clear because of the more complex executable management.

It would be nice for the users of QtCreator if there was a option that could be clicked to turn it on and off.

