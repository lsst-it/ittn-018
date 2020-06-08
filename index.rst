:tocdepth: 1

.. Please do not modify tocdepth; will be fixed when a new Sphinx theme is shipped.

.. sectnum::

.. note::

   **This technote is a work in progress.**

   Description of technote


Introduction
============

Document Overview
-----------------
The purpose of this document is to outline the main Network infrastructure high-level design (HLD) for the Rubin Observatory summit and base sites. It would recommend an architecture based on the requirements set by the project, especially -but not limited to- the Tiger Team in different ICDs and in the documents mentioned in section 1.4.

The intent of this document is to provide an architecture that fulfills the requirements outlined by the project, keeping in mind current needs but also future growth of the network. The HLD does not delve into low-level details (i.e. configuration files, performance analysis, etc...).

As long as the HLD status is draft, it is susceptible to modifications and additions by the IT group or by the request of other subsystems.

After acceptance this document may or not be under change control, and regardless of that this is considered a living document that will be updated upon requirement addition or changes, experiences in the deployment, and operations process.

Scope
-----
- Fulfill the current Network Infrastructure needs of the project and allow for future growth.
- This document focuses only on INTERNAL Network Infrastructure in Chile, it does not cover any Long-Haul Networking (LHN) design beyond the interfaces that connect the internal network in Chile to such system. For information about the LHN design please refer to the latest release of LSE-78.
- While this document mentions firewalls from a functional perspective, it DOES NOT contain specific cybersecurity approaches and neither the firewall hardware.
- This document DOES NOT cover requirements for services such as Wi-Fi and VoIP beyond the interfaces that connect the internal network in Chile to such systems. For information about the aforementioned services please refer to the following documents:
 - Wi-Fi Infrastructure High-Level Design (HLD)
 - VoIP Infrastructure High-Level Design (HLD)
- This document DOES NOT cover requirements for Network Infrastructure integration with other AURA projects.

Assumptions and Caveats
-----------------------
- It is assumed all the project concepts derived from other documents such as those mentioned in section 1.4 and/or the associated ICDs, are correct and remain unchanged.
- It is assumed the reader is familiar with the project Network Infrastructure requirements for Chile. Furthermore, it is also assumed the reader is familiar with Networking technologies such as routers, switches, firewalls, and protocols such as BGP, EVPN, VXLAN, IS-IS, OSPF, etc...
- Even if specific networking solutions and vendors are mentioned in this document, the topological design will be neutral and vendor agnostic. The only exception to this point will be if any of the chosen networking solutions have a requirement that demands a specific topology and will be mentioned as an option. The final topology will be described in the Low-Level Design (LLD) version of this document.

Related Documents
-----------------
- LSE-78 LSST Observatory Network Design
- LPM-122 Information Classification Policy
- LSE-309 Summit to Base ITC Design
- LSE-449 LSST IPv4 Addressing Policy

Technical Solution Overview
============

Data Gathering
--------------
Rubin Observatory in Chile from the beginning of the project did rely on the network infrastructure provided by CTIO CISS to provide internet access to its early users, most of them transitioning from other projects such as SOAR, CTIO, and Gemini, therefore the first development services, for instance, were hosted in CISS's provided networks and servers, which were slowly transitioned to Rubin Observatory's own network infrastructure during the 2015-2017 period, while still very basic and focused just on internet access plus access to some internal resources. The project services such as websites and archive services have always been hosted in Tucson and access to them is over commodity internet without the need for a VPN connection. Early in 2017, as more staff transitioned from Tucson to La Serena and new local staff was being hired, the Rubin Observatory IT North group implemented the first transition network in La Serena, still hosted at the CISS computer room inside the old NOAO south building, and still using the existing Ethernet infrastructure to reach with physical links to the Rubin Observatory offices, but now providing a different network segment and intranet access to more services hosted in Tucson over a site-to-site IPsec VPN, plus an independent link to the Aura Border Router to reach commodity internet without crossing CISS's internal networks. The only exception in this first transition network was the VoIP infrastructure which still relied on CISS.

From this point and on, the Chilean Rubin Observatory networks slowly started to scale with local services such as Active Directory and Exchange, plus archiving for webcams at the construction site. In July 2017 Rubin Observatory decided to procure the first batch of network hardware to become fully independent in terms of network infrastructure during the upcoming years, which required the construction of the new summit and base sites to be finished before this to happen completely.

As the Rubin Observatory summit telescope building and the new base facilities are ready, the following summary requirements must be fulfilled by the new Network Infrastructure:

1. The architecture shall provide design modularity as different modules of the network will be implemented in different stages due to the nature of the construction phases, out of which the summit has the highest priority.
2. The "solution" is defined as a group of technologies, vendors, and hardware chosen to implement the design defined by the Network Architect. The meaning of solution in this document in particular must not refer to a specific product model, as the whole network infrastructure is not implemented by a homogeneous technology stack.
3. The solution must be able to provide wired connectivity to all the areas requiring it as defined in the different ICDs of the subsystems, either via UTP or Fiber Optic ethernet connections. Wi-Fi connectivity is covered in a separate High-Level Design (document).
4. The solution must be able to provide Value-Added Services (VAS) which in case of the network infrastructure translate to services such as Power-over-Ethernet (PoE), Authentication, Authorization and Accounting, traffic filtering and access control, traffic prioritization through Quality of Service (QoS) techniques, monitoring and configuration programmability for eventual integration with software pipelines, etc...
5. The solution shall provide support for standard protocols such as LLDP, OSPF, BGP, STP, etc... as the topological design will be agnostic and vendor-neutral even if part or the totality of the solution is proprietary. This is key to play along with a modular design where parts of the network can be replaced by another model or vendor hardware in case of contingency or due to specific requirements. The solution must also be able to provide full dual-stack IPv4/IPv6 support for its core routing protocols.
6. The solution shall provide methods for redundancy and/or high-availability of the control, management, and data plane where needed.
7. The solution must provide modularity and scalability options for its hardware and software, making possible horizontal and vertical scalability in key devices such as core or spine switches while providing cost savings through a pay-as-you-grow hardware approach. In terms of bandwidth, the solution must be able to scale using transceivers ranging from 1G to 10G in most devices, and 10G to 100G in key devices such as core and spine switches.

Chosen solution
---------------
The decision rationale was a technical analysis of the project requirements by several vendors and distributors held in the 2015/2016 timeframe by the Tiger Team, out of which Cisco Systems was the chosen vendor for most of the LAN, Datacenter, Wi-Fi and VoIP infrastructure. This document will only focus on the LAN and Datacenter infrastructure which build up the backbone of the main network that will connect systems and end-users together. Due to the extensive nature of the solution, containing a very diverse group of devices, the list will be broken up by functional blocks.

Campus Network Hardware
^^^^^^^^^^^^^^^^^^^^^^^
The campus network is a functional block that contains switches where end-users and systems such as IP phones, laptops, printers, Access Point, connect to the network. More detail is provided in section 3.2.

**Distribution Switches**

* **Cisco Catalyst C3850-12XS and 24XS:** Distribution switches whose function is to aggregate the access switches to be installed in the technical rooms all around the buildings, using a 12 port SFP+ version for the base datacenter and a 24 port SFP+ version for the summit. Both models are expanded in capability with a C3850-NM-4-10G module for 4 additional 10G SFP+ ports.

.. figure:: /_static/3850-24XS.JPG
    :name: 3850 24XS
    :width: 500 px

.. figure:: /_static/3850-12XS.JPG
    :name: 3850 12XS
    :width: 500 px

.. .. rubric:: References

.. Make in-text citations with: :cite:`bibkey`.

.. .. bibliography:: local.bib lsstbib/books.bib lsstbib/lsst.bib lsstbib/lsst-dm.bib lsstbib/refs.bib lsstbib/refs_ads.bib
..    :style: lsst_aa
