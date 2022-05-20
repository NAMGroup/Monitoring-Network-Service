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
* The gNodeB is required to have the recommended monitoring software installed.

**The article showcasing the monitoring architecture can be found [here](https://ieeexplore.ieee.org/abstract/document/9647595).**

![](https://ieeexplore.ieee.org/mediastore_new/IEEE/content/media/9647430/9647442/9647595/giann1-p6-giann-small.gif "Monitoring Service Architecture")

## Level 4
Level 4 monitoring refers to cloud infrastructure (bare metal) telemetry. This is achieved by installing the widely used [Prometheus](https://prometheus.io/) on the cloud's bare metal. The Prometheus installation can be done following the [official Prometheus documentation](https://prometheus.io/docs/prometheus/latest/installation/).

## Level 3
Level 3 monitoring refers to telemetry at the MANO layer. The proposed architecture uses [OSM](https://osm.etsi.org/) as the MANO platform. OSM has an integrated [Prometheus](https://osm.etsi.org/docs/user-guide/latest/05-osm-usage.html?highlight=prometheus#monitoring-and-autoscaling), which can then integrate gracefully with the Prometheus at Level 4, as seen in [section 7](#unifying-the-telemetry-sources).  

## Level 2
Level 2 monitoring refers to monitoring of the 5G network. This is realized primarily by gathering metrics from the gNodeB. One approach could be to install another Prometheus at the gNodeB. However, in order to extract metrics that deviate from the default, such as interface bitrate, transport blocks, etc., it is recommended to install the [Netdata](https://www.netdata.cloud/) monitoring software, which offers higher granularity of metrics gathering (up to 1s), as well as the option to add custom plugins to measure specific metrics that aren't measured by default. Additionally, Netdata offers the option to export the metrics in a Prometheus format using its integrated [Prometheus exporter](https://learn.netdata.cloud/docs/agent/exporting/prometheus), which allows a remote Prometheus to then scrape the monitoring data. Information regarding the installation of the Netdata custom plugins, as well as some sample plugins can be found in [this Github repository](https://github.com/NAMGroup/NetData_plugins/tree/5565d60e82691f48f24632cac157df938a357010). 

Additionally, in order to store the data provided by Netdata, an orchestrated Prometheus VNF is used, which plays the role of the timeseries database for the 5G infrastructure data. This can be orchestrated from OSM using the descriptors found in the folder "Descriptors" of this repository. 

## Level 1
Level 2 monitoring refers to VNF level monitoring. In order to monitor the vertical's applications, they need to provide metrics in a Prometheus format. This can be done by having the vertical install their own Prometheus or Prometheus-friendly monitoring software. Otherwise, it is possible to use the ETSI OSM compatible [Juju charms](https://osm.etsi.org/docs/user-guide/latest/05-osm-usage.html?highlight=prometheus#vnf-metrics-indicators). 

## Unifying the telemetry sources
In order to integrate all telemetry sources from all 4 levels into one, which provides a holistic telemetry view, it is possible to either use a central Prometheus instance, which can play the role of hierarchical master to all the previously mentioned Prometheus instances, which can then send its data into a central [Grafana](https://grafana.com/) instance, or the hierarchy architecture can be bypassed and a Grafana instance can scrape the monitoring data from the Prometheus instance at each of Levels 1-4 without a dedicated master Prometheus.
