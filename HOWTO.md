# HOWTO
Here are the basic elements to make the magic happen...
# Create Topology 
1.  Lay out your systems in the editor.
    - Create internal systems for the switches that Apstra will manage:
      
      <img width="351" alt="image" src="https://github.com/user-attachments/assets/8ec4f2cb-ca0c-476a-98ed-bc2814044f0d">
    - edit systems information for system internal, this example :
      
      <img width="209" alt="image" src="https://github.com/user-attachments/assets/26661890-b26a-4c8b-b256-a94a8d9fcd8f">
      
    - Apply system tags to the internal systems to identify leaf and spine
      devices.  These tags should be named "leaf" for leaf devices and
      "spine" for spine devices.
  
## Create Links topology 
2.  Connect your devices.
    - Create link between spine <> leaf, with select device and select port. this example :

       <img width="336" alt="image" src="https://github.com/user-attachments/assets/5b68d9ba-b4e1-476d-a27f-56a1ddc0f17e">

    - This example after add topology spine leaf :
      
      <img width="336" alt="image" src="https://github.com/user-attachments/assets/2230d6ec-ad9a-4cda-a4ad-be44bb1731c2">

    - Host-facing (connect device to external system )links must include either the "mode_access" or "mode_trunk" tags
      to differentiate untagged vs. tagged interfaces.

      <img width="1791" alt="image" src="https://github.com/user-attachments/assets/9af92c9f-04a4-452f-9c99-8e1ef909db6a">

    - Tag the host-facing links with tags to represent the VLANs you want assigned to each host-facing link.  So if you're going to name a VLAN "blue.2800" in your resource group under vlans. select          resource vni (why vni because we need name vlan with bd-vni) then you need a tag assign to target system and interfaces.
      this example :

      <img width="1354" alt="image" src="https://github.com/user-attachments/assets/fda32c4b-23cc-45ed-881f-73133a59b7c8">

      
    
    NOTE:  Where you have Link Aggregation Goups (LAGs) composed of one or more member links, the tags must be applied to the Aggregated
           link, and not the member links.

# Resource Group Part
  Under Resource Management, create the following:
  
    - Group named "lacp" under Root.
    - Group named “underlay” under Root.
    - Group named “overlay” under Root.
    - Group named “clans” under Root.
    - Group named “vrfs” under overlay.

# Resource Allocation Group Part
    Create the following Allocation Groups to be used in the model:
    - “underlay-allocation” of Type ASN
        This is what we'll use to dynamically assign ASN's to each
        internal system.
    - “loopbacks" of Type IPv4
        Used to generate IPv4 addresses for lo0.0 on each internal system.
    - “p2p of Type IPv4
        Used to generate IPv4 addresses for the fabric interfaces.
    - “overlay-allocation” of Type ASN
        This is what we'll use to dynamically assign ASN's to each
    - "LACP System IDs" of Type Integer
        Use low values for integers (e.g., 1-4094).  We'll use these values
        to generate ESI and LACP System ID values.
      
# Resource Allocation Group Part
# Underlay Part
## loopback Part
```
- Resource generator named “ipv4_loopback” under
    Root/underlay/ipv4_loopback
    Scope: node('system', system_type='internal', name='target')
    Allocation Group: loopback
```
<br>
    loopback capture:
      <img src="Images/ipv4_loopback.png" width="75%" height="75%">
 <br>

## point-to-point Part
```
    - Resource generator named “link-underlay-gen” under
      Root/underlay/link-underlay-gen
        - Scope: node('link', role='internal', link_type='aggregate_link', name='target')
        - Allocation Group: p2p
```
<br>
    p2p capture:
        <img src="Images/link-underlay-gen.png" width="75%" height="75%">
<br>

## underlay Part
```
    - Resource generator named “underlay_asn” under
      Root/underlay/underlay_asn
        - Scope:node(' system', system_type='internal', name='target')
        - Allocation Group: underlay_allocation
```
<br>
    underlay_asn capture:
        <img src="Images/underlay_asn.png" width="75%" height="75%">
<br>

## Overlay Part 

## step
```
├── Resource named ‘overlay-asn’ under Root/overlay
    └── Action assign all Internal Device
```
<br>
Overlay Capture :
     <img src="Images/overlay-asn.png" width="75%" height="75%">
<br>
     
# vrf Part
1. create vrf under overlay with name vrfs
   ## (don't change name : vrfs)
   
3. create vrf name example blue

   <img width="1366" alt="image" src="https://github.com/user-attachments/assets/ad95bc2a-d233-4b91-889a-6cf85b9d6824">
  this describe for data vrf :
  ```
  {
  "vrf_leaking": "disable", > enable or disable for use enable forwarding leaking
  "local_pref": "", > put integer (number) manual local pref leaking prefix
  "vrf_parent": [] > forwarding parent table route
 }
  ```
# vlans Part
## Create vlans
```
    - Resource generator named “vlans” under Root
        - Create Group with name example "blue.2800" > blue is name vlan and 2800 is vlan
        - Enter data as below : 
        {
              "bgp_irb": "disable", > disable/enable
              "vrf_name": "blue", > name of vrf
              "peer_ipv4_loopback": "", > peer ipv4 loopback
              "bfd_minimum": "", > bfd parameter
              "peer_asn": "", > peer asn
              "bfd_multiplier": "", > bfd parameter
              "l3": "enable", > disable/enable
              "static_route_irb": "disable", > disable/enable
              "mode_style": "ent-style" > ent-style/sp-style
        }
        - save
```
<br>
Create vlans Capture :
     <img src="Images/create_vlans.png" width="75%" height="75%">
<br>

## Tag vlans
for this vlan tag, the intention is to assign resources which have been explained in the previous part in the Create Links topology part tag vlan. this part only explain how to assign.

select vni resource select assign :

<img width="1335" alt="image" src="https://github.com/user-attachments/assets/7145ce57-1930-4e4c-acf9-b83c2dc02b6c">

select the internal system to be selected:

<img width="1407" alt="image" src="https://github.com/user-attachments/assets/aa4bc1cb-a3e8-44ca-a9d6-1c3582ce749b">

select internal system and select interfaces :

<img width="1836" alt="image" src="https://github.com/user-attachments/assets/3f4f75ea-f4a7-493c-b1fd-fc15d59c5db8">



# Note: don’t changes named


GOOD LUCK!!!
    
    
