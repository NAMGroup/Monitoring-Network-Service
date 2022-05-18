# Monitoring Network Service

1. [Introduction](#introduction)
2. [Disclaimers](#disclaimers)
3. [Level 4](#level-4)
4. [Level 3](#level-3)
5. [Level 2](#level-2)
6. [Level 1](#level-1)
7. [Unifying the telemetetry sources](#unifying-the-telemetry-sources)

## Introduction
This repository refers to a network service that provides a holistic view of the testbed. More importantly it provides monitoring data about:
* The cloud infrastructure
* The MANO platform 
* The orchestrated services
* The gNodeB

## Disclaimers
* This is a service designed to be used for the needs of the University of Patras's [Patras5G](https://wiki.patras5g.eu/) facility, but can be changed to fit other testbed needs.
* This is a VM centric architecture. Work is in progress to move to container centric, but it is not yet ready.
* This implementation uses the [ETSI OSM](https://osm.etsi.org/) as a MANO orchestrator. Other orchestrators can be used, as long as they provide telemetry data in a Prometheus format. 
* In order to receive monitoring data at the application (VNF) level, it is necessary for the application to either provide its own metrics in a Prometheus format, to utilize OSM's Juju charms to extract monitoring data from the VNFs, or to install the recommended monitoring software in the same VM. 
* The gNodeB is required to have the recommended monitoring software installed.

**The article showcasing the monitoring architecture can be found [here](https://ieeexplore.ieee.org/abstract/document/9647595).**

![](https://ieeexplore.ieee.org/mediastore_new/IEEE/content/media/9647430/9647442/9647595/giann1-p6-giann-small.gif "Monitoring Service Architecture")

## Level 4
Level 4 monitoring refers to cloud infrastructure (bare metal) telemetry. This is achieved by installing the widely used [Prometheus](https://prometheus.io/) on the cloud's bare metal. 

## Level 3
Level 3 monitoring refers to telemetry at the MANO layer. The proposed architecture uses OSM as the MANO platform. OSM has an integrated Prometheus, which can then integrate gracefully with the Prometheus at Level 4, as seen at section **X**.  

## Level 2
Level 2 monitoring refers to monitoring of the 5G network. This is realized primarily by gathering metrics from the gNodeB. One approach could be to install another Prometheus at the gNodeB. However, in order to extract metrics that deviate from the default, such as interface bitrate, transport blocks, etc., it is recommended to install the [Netdata](https://www.netdata.cloud/) monitoring software, which offers higher granularity of metrics gathering (up to 1s), as well as the option to add custom plugins to measure specific metrics that aren't measured by default. Additionally, Netdata offers the option to export the metrics in a Prometheus format, which allows a remote Prometheus to then scrape the monitoring data. A sample custom plugin can be found in [XXX](panagiotis).

## Level 1
Level 2 monitoring refers to VNF level monitoring (application)

## Unifying the telemetetry sources
Here we have the full integration  of all telemetry
