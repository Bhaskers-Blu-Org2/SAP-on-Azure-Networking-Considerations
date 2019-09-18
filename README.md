
# Networking Considerations for SAP on Azure

## Problem Statement

SAP on Azure is a very popular workload.  As customers look to deploy their production SAP systems on Azure it is important to consider proper network design to ensure performance.  This document will walk you through how to optimally connect to the Microsoft network for SAP and SAP HANA Large Instance.

## Latency Optimization

When deploying enterprise applications such as SAP in Azure it is important to know the different connectivity methods used with the Microsoft network.  

The most common way to interface with applications hosted in Azure is to connect via the Internet.  Microsoft today interconnects with Internet Service Providers in over 150 locations around the world.  Microsoft provides more than 80 percent of global GDP (Gross Domestic Product)     with an experience of sub-30 milliseconds latency. We are adding new edges every week, and our ambition is to provide this level of performance to all of our global audience.  

![alt text](https://github.com/jaimesc/images/blob/master/EdgeSites.png)

When using Internet connectivity to access SAP applications customers can either leverage Microsoft VPN Gateway or Azure Virtual WAN.  VPN Gateway allows customers to establish an IPSEC tunnel from an on-premise device to Azure directly over the internet.  

Learn how to deploy VPN Gateway here: https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways 

Azure Virtual WAN is a solution that allows for a managed hub virtual network that will be a central connection point for IPSEC tunnels and ExpressRoute connections.  Azure Virtual WAN is a more scalable VPN solution that can support more tunnels and aggregate throughput than a VPN Gateway.  

Learn how to deploy Azure Virtual WAN here: https://docs.microsoft.com/en-us/azure/virtual-wan/ 

Azure ExpressRoute is a private connection to the Microsoft network.  Microsoft has 200+ partners around the world that can deliver connections to one or many of our 50+ ExpressRoute locations. Customers can either leverage connections that network partners have pre-established with Microsoft or through a cloud exchange partner.  Customer also have the option to connect with Microsoft ExpressRoute routers (MSEE) directly using ExpressRoute Direct.  These connections can be done at either 10 Gbps or 100 Gbps speeds. ExpressRoute has two different routing domains or peerings. Microsoft peering is used to access Azure platform services, Dynamics 365 and Office 365 (with approval) using public IP addresses. In SAP deployments, Azure private peering is used to access SAP Virtual Machines or HANA Large Instance systems using private IP addresses.  

![alt text](https://github.com/jaimesc/images/blob/master/ERSites.png)

Learn how to deploy ExpressRoute here: https://docs.microsoft.com/en-us/azure/expressroute/ 

When deploying any hybrid connectivity solution, it’s important to look at three factors.  
    1.	Where traffic is originating from? i.e. Which Customer site(s)?
    2.	Where traffic is destined to? i.e. Which Azure Region(s)?
    3.	Where is it entering the Microsoft network? i.e. Which Internet Edge or ExpressRoute site?
    
In most enterprises, there is a mix of connectivity options (MPLS, Internet, SDWAN).  Microsoft Azure can support these different connectivity models for enterprise applications like SAP.  In order to connect to the Microsoft network via any connectivity model, a network service provider will need to be involved to deliver connectivity from the customer site(s) to the Microsoft Network Edge.  You can use information from the provider to create a map of how traffic will reach the Microsoft Network.  Its preferred that you enter the Microsoft network as close to customer sites as possible.  This will allow for optimal routing on the Microsoft backbone to the Azure region(s) where your SAP workloads exist.  Customers will need to work with their providers to understand where they connect to Microsoft over the Internet or over private connections for ExpressRoute.

Learn more about Microsoft global backbone network here: https://azure.microsoft.com/en-us/global-infrastructure/global-network/ 

## HANA Large Instance network considerations

SAP HANA Large Instance is dedicated hardware in Azure.  The HANA Large Instance infrastructure comes with its own ExpressRoute circuit managed by Microsoft. This circuit cost is built into the infrastructure cost for HANA Large Instance.  

Learn more about SAP HANA Large Instance here: https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture 

This Microsoft managed circuit connects a customer’s virtual network in Azure with the HANA Large Instance environment.  Customers can allow private access to SAP HLI on Azure by creating an ExpressRoute circuit and enabling the Global Reach add-on.  The ExpressRoute Global Reach add-on allows for a customer’s ExpressRoute circuit to peer with the Microsoft managed ExpressRoute circuit for HLI and allow for full transit routing from the customer site to the HLI environment in Azure.

ExpressRoute can be used for high bandwidth, predictable connectivity from a customer private MPLS network.  Customers who have branch offices that need encrypted connectivity to SAP HLI over the Internet can use Azure Virtual WAN.  Azure Virtual WAN is a scalable VPN solution where customers can leverage existing VPN appliances and connect to the Azure Virtual WAN Hub.  This hub can be used for all hybrid connectivity. This includes the Microsoft managed ExpressRoute circuit, the customer managed ExpressRoute circuit and any IPSEC VPN tunnel terminated in the vWAN Hub.  ExpressRoute circuits connected to the Azure vWAN hub require the Global Reach add-on to be enabled.  This allows transit communications into the HLI infrastructure regardless if the traffic originates from a customer site connected via ExpressRoute or an IPSEC VPN tunnel.

Learn more about ExpressRoute Global Reach here: https://docs.microsoft.com/en-us/azure/expressroute/expressroute-global-reach 

![alt text](https://github.com/jaimesc/images/blob/master/SAPNet.png)
