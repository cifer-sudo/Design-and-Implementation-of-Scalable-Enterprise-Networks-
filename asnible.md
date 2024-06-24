# Using deleted

# Before state:
# -------------
#
# router-ios#sh running-config | section ^interface
# interface GigabitEthernet0/0
# interface GigabitEthernet0/1
#  ipv6 ospf 55 area 105
#  ipv6 ospf priority 20
#  ipv6 ospf transmit-delay 30
#  ipv6 ospf adjacency stagger disable
# interface GigabitEthernet0/2
#  ip ospf priority 40
#  ip ospf adjacency stagger disable
#  ip ospf ttl-security hops 50
#  ip ospf 10 area 20
#  ip ospf cost 30

- name: Delete provided OSPF Interface config
  cisco.ios.ios_ospf_interfaces:
    config:
      - name: GigabitEthernet0/1
    state: deleted

# Task Output:
# ------------

# before:
#   - name: GigabitEthernet0/0
#   - address_family:
#     - adjacency: true
#       afi: ipv6
#       priority: 20
#       process:
#         area_id: '105'
#         id: 55
#       transmit_delay: 30
#     name: GigabitEthernet0/1
#   - address_family:
#     - adjacency: true
#       afi: ipv4
#       cost:
#         interface_cost: 30
#       priority: 40
#       process:
#         area_id: '20'
#         id: 10
#       ttl_security:
#         hops: 50
#         set: true
#     name: GigabitEthernet0/2
#
# commands:
# - interface GigabitEthernet0/1
# - no ipv6 ospf 55 area 105
# - no ipv6 ospf adjacency stagger disable
# - no ipv6 ospf priority 20
# - no ipv6 ospf transmit-delay 30
#
# after:
#   - name: GigabitEthernet0/0
#   - name: GigabitEthernet0/1
#   - address_family:
#     - adjacency: true
#       afi: ipv4
#       cost:
#         interface_cost: 30
#       priority: 40
#       process:
#         area_id: '20'
#         id: 10
#       ttl_security:
#         hops: 50
#         set: true
#     name: GigabitEthernet0/2
#
# After state:
# ------------
# router-ios#sh running-config | section ^interface
# interface GigabitEthernet0/0
# interface GigabitEthernet0/1
# interface GigabitEthernet0/2
#  ip ospf priority 40
#  ip ospf adjacency stagger disable
#  ip ospf ttl-security hops 50
#  ip ospf 10 area 20
#  ip ospf cost 30

# Using deleted without any config passed (NOTE: This will delete all OSPF Interfaces configuration from device)

# Before state:
# -------------
#
# router-ios#sh running-config | section ^interface
# interface GigabitEthernet0/0
# interface GigabitEthernet0/1
#  ipv6 ospf 55 area 105
#  ipv6 ospf priority 20
#  ipv6 ospf transmit-delay 30
#  ipv6 ospf adjacency stagger disable
# interface GigabitEthernet0/2
#  ip ospf priority 40
#  ip ospf adjacency stagger disable
#  ip ospf ttl-security hops 50
#  ip ospf 10 area 20
#  ip ospf cost 30

- name: Delete all OSPF config from interfaces
  cisco.ios.ios_ospf_interfaces:
    state: deleted

# Task Output:
# ------------

# before:
#   - name: GigabitEthernet0/0
#   - address_family:
#     - adjacency: true
#       afi: ipv6
#       priority: 20
#       process:
#         area_id: '105'
#         id: 55
#       transmit_delay: 30
#     name: GigabitEthernet0/1
#   - address_family:
#     - adjacency: true
#       afi: ipv4
#       cost:
#         interface_cost: 30
#       priority: 40
#       process:
#         area_id: '20'
#         id: 10
#       ttl_security:
#         hops: 50
#         set: true
#     name: GigabitEthernet0/2
#
# commands:
# - interface GigabitEthernet0/2
# - no ip ospf 10 area 20
# - no ip ospf adjacency stagger disable
# - no ip ospf cost 30
# - no ip ospf priority 40
# - no ip ospf ttl-security hops 50
# - interface GigabitEthernet0/1
# - no ipv6 ospf 55 area 105
# - no ipv6 ospf adjacency stagger disable
# - no ipv6 ospf priority 20
# - no ipv6 ospf transmit-delay 30
#
# after:
#   - name: GigabitEthernet0/0
#   - name: GigabitEthernet0/1
#   - name: GigabitEthernet0/2

# After state:
# -------------
# router-ios#sh running-config | section ^interface
# interface GigabitEthernet0/0
# interface GigabitEthernet0/1
# interface GigabitEthernet0/2

# Using merged

# Before state:
# -------------
#
# router-ios#sh running-config | section ^interface
# router-ios#

- name: Merge provided OSPF Interfaces configuration
  cisco.ios.ios_ospf_interfaces:
    config:
      - name: GigabitEthernet0/1
        address_family:
          - afi: ipv4
            process:
              id: 10
              area_id: 30
            adjacency: true
            bfd: true
            cost:
              interface_cost: 5
            dead_interval:
              time: 5
            demand_circuit:
              ignore: true
            network:
              broadcast: true
            priority: 25
            resync_timeout: 10
            shutdown: true
            ttl_security:
              hops: 50
          - afi: ipv6
            process:
              id: 35
              area_id: 45
            adjacency: true
            database_filter: true
            manet:
              link_metrics:
                cost_threshold: 10
            priority: 55
            transmit_delay: 45
    state: merged

# Task Output:
# ------------
# before: []
#
# commands:
# - interface GigabitEthernet0/1
# - ip ospf 10 area 30
# - ip ospf adjacency stagger disable
# - ip ospf bfd
# - ip ospf cost 5
# - ip ospf dead-interval 5
# - ip ospf demand-circuit ignore
# - ip ospf network broadcast
# - ip ospf priority 25
# - ip ospf resync-timeout 10
# - ip ospf shutdown
# - ip ospf ttl-security hops 50
# - ipv6 ospf 35 area 45
# - ipv6 ospf adjacency stagger disable
# - ipv6 ospf database-filter all out
# - ipv6 ospf manet peering link-metrics 10
# - ipv6 ospf priority 55
# - ipv6 ospf transmit-delay 45
#
# after:
#   - name: GigabitEthernet0/0
#   - address_family:
#     - adjacency: true
#       afi: ipv4
#       bfd: true
#       cost:
#         interface_cost: 5
#       dead_interval:
#         time: 5
#       demand_circuit:
#         enable: true
#         ignore: true
#       network:
#         broadcast: true
#       priority: 25
#       process:
#         area_id: '30'
#         id: 10
#       resync_timeout: 10
#       shutdown: true
#       ttl_security:
#         hops: 50
#         set: true
#     - adjacency: true
#       afi: ipv6
#       database_filter: true
#       manet:
#         link_metrics:
#           cost_threshold: 10
#       priority: 55
#       process:
#         area_id: '45'
#         id: 35
#       transmit_delay: 45
#     name: GigabitEthernet0/1
#   - name: GigabitEthernet0/2

# After state:
# -------------
#
# router-ios#sh running-config | section ^interface
# interface GigabitEthernet0/0
# interface GigabitEthernet0/1
#  ip ospf network broadcast
#  ip ospf resync-timeout 10
#  ip ospf dead-interval 5
#  ip ospf priority 25
#  ip ospf demand-circuit ignore
#  ip ospf bfd
#  ip ospf adjacency stagger disable
#  ip ospf ttl-security hops 50
#  ip ospf shutdown
#  ip ospf 10 area 30
#  ip ospf cost 5
#  ipv6 ospf 35 area 45
#  ipv6 ospf priority 55
#  ipv6 ospf transmit-delay 45
#  ipv6 ospf database-filter all out
#  ipv6 ospf adjacency stagger disable
#  ipv6 ospf manet peering link-metrics 10
# interface GigabitEthernet0/2

# Using overridden

# Before state:
# -------------
#
# router-ios#sh running-config | section ^interface
# interface GigabitEthernet0/0
# interface GigabitEthernet0/1
#  ip ospf network broadcast
#  ip ospf resync-timeout 10
#  ip ospf dead-interval 5
#  ip ospf priority 25
#  ip ospf demand-circuit ignore
#  ip ospf bfd
#  ip ospf adjacency stagger disable
#  ip ospf ttl-security hops 50
#  ip ospf shutdown
#  ip ospf 10 area 30
#  ip ospf cost 5
#  ipv6 ospf 35 area 45
#  ipv6 ospf priority 55
#  ipv6 ospf transmit-delay 45
#  ipv6 ospf database-filter all out
#  ipv6 ospf adjacency stagger disable
#  ipv6 ospf manet peering link-metrics 10
# interface GigabitEthernet0/2

- name: Override running config with provided OSPF Interfaces configuration
  cisco.ios.ios_ospf_interfaces:
    config:
      - name: GigabitEthernet0/1
        address_family:
          - afi: ipv6
            process:
              id: 55
              area_id: 105
            adjacency: true
            priority: 20
            transmit_delay: 30
      - name: GigabitEthernet0/2
        address_family:
          - afi: ipv4
            process:
              id: 10
              area_id: 20
            adjacency: true
            cost:
              interface_cost: 30
            priority: 40
            ttl_security:
              hops: 50
    state: overridden

# Task Output:
# ------------
#
# before:
#   - name: GigabitEthernet0/0
#   - address_family:
#     - adjacency: true
#       afi: ipv4
#       bfd: true
#       cost:
#         interface_cost: 5
#       dead_interval:
#         time: 5
#       demand_circuit:
#         enable: true
#         ignore: true
#       network:
#         broadcast: true
#       priority: 25
#       process:
#         area_id: '30'
#         id: 10
#       resync_timeout: 10
#       shutdown: true
#       ttl_security:
#         hops: 50
#         set: true
#     - adjacency: true
#       afi: ipv6
#       database_filter: true
#       manet:
#         link_metrics:
#           cost_threshold: 10
#       priority: 55
#       process:
#         area_id: '45'
#         id: 35
#       transmit_delay: 45
#     name: GigabitEthernet0/1
#   - name: GigabitEthernet0/2
#
# commands:
# - interface GigabitEthernet0/2
# - ip ospf 10 area 20
# - ip ospf adjacency stagger disable
# - ip ospf cost 30
# - ip ospf priority 40
# - ip ospf ttl-security hops 50
# - interface GigabitEthernet0/1
# - ipv6 ospf 55 area 105
# - no ipv6 ospf database-filter all out
# - no ipv6 ospf manet peering link-metrics 10
# - ipv6 ospf priority 20
# - ipv6 ospf transmit-delay 30
# - no ip ospf 10 area 30
# - no ip ospf adjacency stagger disable
# - no ip ospf bfd
# - no ip ospf cost 5
# - no ip ospf dead-interval 5
# - no ip ospf demand-circuit ignore
# - no ip ospf network broadcast
# - no ip ospf priority 25
# - no ip ospf resync-timeout 10
# - no ip ospf shutdown
# - no ip ospf ttl-security hops 50
#
# parsed:
#   - name: GigabitEthernet0/0
#   - address_family:
#     - adjacency: true
#       afi: ipv6
#       priority: 20
#       process:
#         area_id: '105'
#         id: 55
#       transmit_delay: 30
#     name: GigabitEthernet0/1
#   - address_family:
#     - adjacency: true
#       afi: ipv4
#       cost:
#         interface_cost: 30
#       priority: 40
#       process:
#         area_id: '20'
#         id: 10
#       ttl_security:
#         hops: 50
#         set: true
#     name: GigabitEthernet0/2
#
# After state:
# -------------
#
# router-ios#sh running-config | section ^interface
# interface GigabitEthernet0/0
# interface GigabitEthernet0/1
#  ipv6 ospf 55 area 105
#  ipv6 ospf priority 20
#  ipv6 ospf transmit-delay 30
#  ipv6 ospf adjacency stagger disable
# interface GigabitEthernet0/2
#  ip ospf priority 40
#  ip ospf adjacency stagger disable
#  ip ospf ttl-security hops 50
#  ip ospf 10 area 20
#  ip ospf cost 30

# Using replaced

# Before state:
# -------------
#
# router-ios#sh running-config | section ^interface
# interface GigabitEthernet0/0
# interface GigabitEthernet0/1
#  ip ospf network broadcast
#  ip ospf resync-timeout 10
#  ip ospf dead-interval 5
#  ip ospf priority 25
#  ip ospf demand-circuit ignore
#  ip ospf bfd
#  ip ospf adjacency stagger disable
#  ip ospf ttl-security hops 50
#  ip ospf shutdown
#  ip ospf 10 area 30
#  ip ospf cost 5
#  ipv6 ospf 35 area 45
#  ipv6 ospf priority 55
#  ipv6 ospf transmit-delay 45
#  ipv6 ospf database-filter all out
#  ipv6 ospf adjacency stagger disable
#  ipv6 ospf manet peering link-metrics 10
# interface GigabitEthernet0/2

- name: Replaced provided OSPF Interfaces configuration
  cisco.ios.ios_ospf_interfaces:
    config:
      - name: GigabitEthernet0/2
        address_family:
          - afi: ipv6
            process:
              id: 55
              area_id: 105
            adjacency: true
            priority: 20
            transmit_delay: 30
    state: replaced

# Task Output:
# ------------
#
# before:
#   - name: GigabitEthernet0/0
#   - address_family:
#     - adjacency: true
#       afi: ipv4
#       bfd: true
#       cost:
#         interface_cost: 5
#       dead_interval:
#         time: 5
#       demand_circuit:
#         enable: true
#         ignore: true
#       network:
#         broadcast: true
#       priority: 25
#       process:
#         area_id: '30'
#         id: 10
#       resync_timeout: 10
#       shutdown: true
#       ttl_security:
#         hops: 50
#         set: true
#     - adjacency: true
#       afi: ipv6
#       database_filter: true
#       manet:
#         link_metrics:
#           cost_threshold: 10
#       priority: 55
#       process:
#         area_id: '45'
#         id: 35
#       transmit_delay: 45
#     name: GigabitEthernet0/1
#   - name: GigabitEthernet0/2
#
# commands:
# - interface GigabitEthernet0/2
# - ipv6 ospf 55 area 105
# - ipv6 ospf adjacency stagger disable
# - ipv6 ospf priority 20
# - ipv6 ospf transmit-delay 30
#
# after:
#   - name: GigabitEthernet0/0
#   - address_family:
#     - adjacency: true
#       afi: ipv4
#       bfd: true
#       cost:
#         interface_cost: 5
#       dead_interval:
#         time: 5
#       demand_circuit:
#         enable: true
#         ignore: true
#       network:
#         broadcast: true
#       priority: 25
#       process:
#         area_id: '30'
#         id: 10
#       resync_timeout: 10
#       shutdown: true
#       ttl_security:
#         hops: 50
#         set: true
#     - adjacency: true
#       afi: ipv6
#       database_filter: true
#       manet:
#         link_metrics:
#           cost_threshold: 10
#       priority: 55
#       process:
#         area_id: '45'
#         id: 35
#       transmit_delay: 45
#     name: GigabitEthernet0/1
#   - address_family:
#     - adjacency: true
#       afi: ipv6
#       priority: 20
#       process:
#         area_id: '105'
#         id: 55
#       transmit_delay: 30
#     name: GigabitEthernet0/2

# After state:
# ------------
# router-ios#sh running-config | section ^interface
# interface GigabitEthernet0/0
# interface GigabitEthernet0/1
#  ip ospf network broadcast
#  ip ospf resync-timeout 10
#  ip ospf dead-interval 5
#  ip ospf priority 25
#  ip ospf demand-circuit ignore
#  ip ospf bfd
#  ip ospf adjacency stagger disable
#  ip ospf ttl-security hops 50
#  ip ospf shutdown
#  ip ospf 10 area 30
#  ip ospf cost 5
#  ipv6 ospf 35 area 45
#  ipv6 ospf priority 55
#  ipv6 ospf transmit-delay 45
#  ipv6 ospf database-filter all out
#  ipv6 ospf adjacency stagger disable
#  ipv6 ospf manet peering link-metrics 10
# interface GigabitEthernet0/2
#  ipv6 ospf 55 area 105
#  ipv6 ospf priority 20
#  ipv6 ospf transmit-delay 30
#  ipv6 ospf adjacency stagger disable

# Using Gathered

# Before state:
# -------------
#
# router-ios#sh running-config | section ^interface
# interface GigabitEthernet0/0
# interface GigabitEthernet0/1
#  ip ospf network broadcast
#  ip ospf resync-timeout 10
#  ip ospf dead-interval 5
#  ip ospf priority 25
#  ip ospf demand-circuit ignore
#  ip ospf bfd
#  ip ospf adjacency stagger disable
#  ip ospf ttl-security hops 50
#  ip ospf shutdown
#  ip ospf 10 area 30
#  ip ospf cost 5
#  ipv6 ospf 35 area 45
#  ipv6 ospf priority 55
#  ipv6 ospf transmit-delay 45
#  ipv6 ospf database-filter all out
#  ipv6 ospf adjacency stagger disable
#  ipv6 ospf manet peering link-metrics 10
# interface GigabitEthernet0/2

- name: Gather OSPF Interfaces provided configurations
  cisco.ios.ios_ospf_interfaces:
    config:
    state: gathered

# Task Output:
# ------------
#
# gathered:
#   - name: GigabitEthernet0/0
#   - address_family:
#     - adjacency: true
#       afi: ipv4
#       bfd: true
#       cost:
#         interface_cost: 5
#       dead_interval:
#         time: 5
#       demand_circuit:
#         enable: true
#         ignore: true
#       network:
#         broadcast: true
#       priority: 25
#       process:
#         area_id: '30'
#         id: 10
#       resync_timeout: 10
#       shutdown: true
#       ttl_security:
#         hops: 50
#         set: true
#     - adjacency: true
#       afi: ipv6
#       database_filter: true
#       manet:
#         link_metrics:
#           cost_threshold: 10
#       priority: 55
#       process:
#         area_id: '45'
#         id: 35
#       transmit_delay: 45
#     name: GigabitEthernet0/1
#   - name: GigabitEthernet0/2

# After state:
# ------------
#
# router-ios#sh running-config | section ^interface
# interface GigabitEthernet0/0
# interface GigabitEthernet0/1
#  ip ospf network broadcast
#  ip ospf resync-timeout 10
#  ip ospf dead-interval 5
#  ip ospf priority 25
#  ip ospf demand-circuit ignore
#  ip ospf bfd
#  ip ospf adjacency stagger disable
#  ip ospf ttl-security hops 50
#  ip ospf shutdown
#  ip ospf 10 area 30
#  ip ospf cost 5
#  ipv6 ospf 35 area 45
#  ipv6 ospf priority 55
#  ipv6 ospf transmit-delay 45
#  ipv6 ospf database-filter all out
#  ipv6 ospf adjacency stagger disable
#  ipv6 ospf manet peering link-metrics 10
# interface GigabitEthernet0/2

# Using Rendered

- name: Render the commands for provided  configuration
  cisco.ios.ios_ospf_interfaces:
    config:
      - name: GigabitEthernet0/1
        address_family:
          - afi: ipv4
            process:
              id: 10
              area_id: 30
            adjacency: true
            bfd: true
            cost:
              interface_cost: 5
            dead_interval:
              time: 5
            demand_circuit:
              ignore: true
            network:
              broadcast: true
            priority: 25
            resync_timeout: 10
            shutdown: true
            ttl_security:
              hops: 50
          - afi: ipv6
            process:
              id: 35
              area_id: 45
            adjacency: true
            database_filter: true
            manet:
              link_metrics:
                cost_threshold: 10
            priority: 55
            transmit_delay: 45
    state: rendered

# Task Output:
# ------------
#
# rendered:
# - interface GigabitEthernet0/1
# - ip ospf 10 area 30
# - ip ospf adjacency stagger disable
# - ip ospf bfd
# - ip ospf cost 5
# - ip ospf dead-interval 5
# - ip ospf demand-circuit ignore
# - ip ospf network broadcast
# - ip ospf priority 25
# - ip ospf resync-timeout 10
# - ip ospf shutdown
# - ip ospf ttl-security hops 50
# - ipv6 ospf 35 area 45
# - ipv6 ospf adjacency stagger disable
# - ipv6 ospf database-filter all out
# - ipv6 ospf manet peering link-metrics 10
# - ipv6 ospf priority 55
# - ipv6 ospf transmit-delay 45
#

# Using Parsed

# File: parsed.cfg
# ----------------
#
# interface GigabitEthernet0/2
# interface GigabitEthernet0/1
#  ip ospf network broadcast
#  ip ospf resync-timeout 10
#  ip ospf dead-interval 5
#  ip ospf priority 25
#  ip ospf demand-circuit ignore
#  ip ospf bfd
#  ip ospf adjacency stagger disable
#  ip ospf ttl-security hops 50
#  ip ospf shutdown
#  ip ospf 10 area 30
#  ip ospf cost 5
#  ipv6 ospf 35 area 45
#  ipv6 ospf priority 55
#  ipv6 ospf transmit-delay 45
#  ipv6 ospf database-filter all out
#  ipv6 ospf adjacency stagger disable
#  ipv6 ospf manet peering link-metrics 10
# interface GigabitEthernet0/0

- name: Parse the provided configuration with the existing running configuration
  cisco.ios.ios_ospf_interfaces:
    running_config: "{{ lookup('file', 'parsed.cfg') }}"
    state: parsed

# Task Output:
# ------------
#
# parsed:
#   - name: GigabitEthernet0/2
#   - address_family:
#     - adjacency: true
#       afi: ipv4
#       bfd: true
#       cost:
#         interface_cost: 5
#       dead_interval:
#         time: 5
#       demand_circuit:
#         enable: true
#         ignore: true
#       network:
#         broadcast: true
#       priority: 25
#       process:
#         area_id: '30'
#         id: 10
#       resync_timeout: 10
#       shutdown: true
#       ttl_security:
#         hops: 50
#         set: true
#     - adjacency: true
#       afi: ipv6
#       database_filter: true
#       manet:
#         link_metrics:
#           cost_threshold: 10
#       priority: 55
#       process:
#         area_id: '45'
#         id: 35
#       transmit_delay: 45
#     name: GigabitEthernet0/1
#   - name: GigabitEthernet0/0
