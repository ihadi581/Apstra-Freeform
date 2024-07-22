# HOWTO
Here are the basic elements to make the magic happen...

1.  Lay out your systems in the editor.
    - Create internal systems for the switches that Apstra will manage.
    - Create external systems for hosts and other devices that will connect
      to the edge of the fabric.
    - Apply system tags to the internal systems to identify leaf and spine
      devices.  These tags should be named "leaf" for leaf devices and
      "spine" for spine devices.

2.  Connect your devices.
    - Fabric links must include the tag "fab_link".
    - Host-facing links must include either the "mode_access" or
      "mode_trunk" tags to differentiate untagged vs. tagged interfaces.
    - Tag the host-facing links with tags to represent the VLANs you
      want assigned to each host-facing link.  So if you're going to name
      a VLAN "vlan_100" in your property set (we'll discuss that later on),
      then you need a tag named "vlan_100" applied to the appropriate
      host interface.
    
    NOTE:  Where you have Link Aggregation Goups (LAGs) composed of one
           or more member links, the tags must be applied to the Aggregated
           link, and not the member links.

3.  Create the following Allocation Groups to be used in the model:
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

4.  Under Resource Management, create the following:
    - Group named "lacp" under Root.
    - Group named “underlay” under Root.
    - Group named “overlay” under Root.
    - Group named “clans” under Root.
    - Group named “vrfs” under overlay.
   # Underlay Part
    - Resource generator named “ipv4_loopback” under
      Root/underlay/ipv4_loopback
        - Scope: node('system', system_type='internal', name='target')
        - Allocation Group: loopback
    - Resource generator named “link-underlay-gen” under
      Root/underlay/link-underlay-gen
        - Scope: node('link', role='internal', link_type='aggregate_link', name='target')
        - Allocation Group: p2p
    - Resource generator named “underlay_asn” under
      Root/underlay/underlay_asn
        - Scope:node(' system', system_type='internal', name='target')
        - Allocation Group: underlay_allocation
# Overlay Part
     - Resource named ‘overlay-asn’ under Root/overlay
     - Action assign all Internal Device


# Note don’t changes named


GOOD LUCK!!!
    
    
