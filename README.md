# ansible-nimble-streamer

[![Ansible Galaxy](https://img.shields.io/ansible/role/23393.svg)](https://galaxy.ansible.com/fpm-git/nimble-streamer/)
 
 - Ansible 2.1+ recommended
 - Currently compatible with CentOS 7.x

## Contents

 1. [Installation](#installation)
 2. [Getting Started](#getting-started)
 3. [Role Variables](#role-variables)
## Installation

``` bash
$ ansible-galaxy install fpm-git.nimble-streamer
```

## Getting started

This role expects to be run as root or as a user with sudo privileges.

Here's an example playbook with some firewall tasks to open ports for nimble streamer
``` yml
---
- name: configure nimble streamers 
  hosts: edge_server
  become: yes # Become root
  vars:
    - nimble_auto_update: true
    - nimble_transcoder: true
    - nimble_wmspanel_username: <Your WMSPanel username>
    - nimble_wmspanel_password: <Your WMSPanel password>
  roles:
    - fpm-git.nimble-streamer
  tasks: 
    # ETH0 (Internet)
    - firewalld:
        zone: public
        interface: eth0
        permanent: true
        immediate: true
        state: enabled
    - firewalld:
        zone: public
        service: ssh
        permanent: true
        immediate: true
        state: disabled
    # Nimble HTTP port
    - firewalld:
        zone: public
        port: "{{ nimble_port }}/tcp"
        permanent: true
        immediate: true
        state: enabled
    # Nimble HTTPS port
    - firewalld:
        zone: public
        port: "{{ nimble_ssl_port }}/tcp"
        permanent: true
        immediate: true
        state: enabled
      when: nimble_ssl
        
    # ETH1 (Private Network)
    - firewalld:
        zone: internal
        interface: eth1
        permanent: true
        immediate: true
        state: enabled
    - firewalld:
        zone: internal
        service: ssh
        permanent: true
        immediate: true
        state: enabled
    # Nimble HTTP port
    - firewalld:
        zone: internal
        port: "{{ nimble_port }}/tcp"
        permanent: true
        immediate: true
        state: enabled
    # Nimble HTTPS port
    - firewalld:
        zone: internal
        port: "{{ nimble_ssl_port }}/tcp"
        permanent: true
        immediate: true
        state: enabled
      when: nimble_ssl
```

## Role Variables

Here is a list of all the default variables for this role, which are also available in defaults/main.yml.

``` yml
---
###################################
# Ansible task options
###################################
nimble_streamer_version:
nimble_force_service_restart: false
nimble_auto_update: false
nimble_transcoder: false
nimble_run_as_root: false
nimble_user: nimble

nimble_management_api: false

nimble_ssl: false
nimble_ssl_local_certificate:
nimble_ssl_local_certificate_key:

###################################
# Nimble Streamer configuation
#
# doc: http://blog.wmspanel.com/p/nimble-streamer-configuration.html
###################################
nimble_port: 80
nimble_worker_threads: 1
nimble_transmuxer_threads:
nimble_listen_interfaces: "*"
nimble_enable_ipv6: false
nimble_restrict_session_ip: false
nimble_cdn_origin:
nimble_user_agent:
nimble_session_timeout:
nimble_pass_headers:
nimble_passed_header_prefix:
nimble_absolute_path:
nimble_update_geoip_database:
nimble_custom_pd_exts:
nimble_enable_pd_for_remote_vod:
nimble_vod_subtitle_default_language_id:
nimble_mpegts_for_audio_vod:
nimble_mpegts_inactivity_check_interval:
nimble_udp_so_receive_buffer:

nimble_video_index:
nimble_audio_index:

# Log Configuration
nimble_logging: info
nimble_log_access:
nimble_log_sessions:
nimble_max_log_file_size:
nimble_max_log_files_to_keep:

# CORS Configuration
nimble_access_control_allow_origin:
nimble_access_control_allow_credentials:
nimble_access_control_expose_headers:
nimble_access_control_allow_headers:
nimble_crossdomain_xml_allow_access_from_domain:
nimble_crossdomain_xml:

# HLS related Configuration
nimble_hls_add_program_date_time:
nimble_hls_add_closed_caption_none:

# RTMP related Configuration
nimble_rtmp_max_viewers:
nimble_rtmp_working_thread:
nimble_rtmp_buffer_items:

# Websocket related Configuration
nimble_websocket_live_max_viewers:
nimble_websocket_live_worker_threads:

# Cache related Configuration
nimble_cache_path: "/var/cache/nimble"
nimble_max_cache_size: 512
nimble_max_disk_cache_size: 4096
nimble_vod_cache_timeout:
nimble_vod_cache_min_storage_time:
nimble_vod_chunk_duration:
nimble_vod_to_ram_cache_first:
nimble_ram_cache_for_vod_threshold:
nimble_playlist_cache_control:
nimble_chunk_cache_control:

# Management API Configuration
nimble_management_port: 8090
nimble_management_listen_interfaces: "*"
nimble_management_token: 

# Nimble Streamer SSL Configuration
nimble_ssl_port: 443
nimble_ssl_certificate: 
nimble_ssl_certificate_key: 

# Nimble Streamer WMSPanel Configuration
nimble_wmspanel_username: 
nimble_wmspanel_password: 
nimble_recovery_id:

```