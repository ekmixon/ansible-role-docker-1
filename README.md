# ansible-role-docker

[![Build Status](https://travis-ci.org/linuxhq/ansible-role-docker.svg?branch=master)](https://travis-ci.org/linuxhq/ansible-role-docker)
[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-docker-blue.svg?style=flat)](https://galaxy.ansible.com/linuxhq/docker)
[![License](https://img.shields.io/badge/license-GPLv3-brightgreen.svg?style=flat)](COPYING)

RHEL/CentOS - Docker Community Edition

## Requirements

None

## Role Variables

Available variables are listed below, along with default values:

    docker_compose_app: []
    docker_compose_bin: /usr/bin/docker-compose
    docker_compose_dir: /etc/docker/compose
    docker_compose_env: []
    docker_containers: []
    docker_kmods:
      - iptable_filter
      - iptable_nat
      - nf_conntrack_netlink
      - nf_nat
      - overlay
      - tun
      - veth
      - xt_addrtype
      - xt_conntrack
      - xt_MASQUERADE
      - xt_nat
    docker_networks: []
    docker_packages:
      - containerd.io
      - docker-ce
      - docker-compose
    docker_repository_stable: true
    docker_repository_stable_debuginfo: false
    docker_repository_stable_source: false
    docker_repository_test: false
    docker_repository_test_debuginfo: false
    docker_repository_test_source: false
    docker_repository_nightly: false
    docker_repository_nightly_debuginfo: false
    docker_repository_nightly_source: false
    docker_systemd:
      - containerd.service
      - docker.service
    docker_users: []

## Dependencies

None

## Example Playbook

    - hosts: servers
      roles:
        - role: linuxhq.docker
          docker_compose_env:
            - name: watchtower
              env:
                LINUXHQ_TZ: America/Los_Angeles
                LINUXHQ_UMASK: 2
                LINUXHQ_WATCHTOWER_CLEANUP: 'true'
                LINUXHQ_WATCHTOWER_POLL_INTERVAL: 3600
          docker_compose_app:
            - name: watchtower
              definition:
                version: '3'
                networks:
                  isolated_30:
                    driver: bridge
                    ipam:
                      config:
                        - subnet: 192.168.0.0/30
                services:
                  watchtower:
                    container_name: watchtower
                    environment:
                      WATCHTOWER_CLEANUP: ${LINUXHQ_WATCHTOWER_CLEANUP}
                      WATCHTOWER_POLL_INTERVAL: ${LINUXHQ_WATCHTOWER_POLL_INTERVAL}
                      TZ: ${LINUXHQ_TZ}
                      UMASK: ${LINUXHQ_UMASK}
                    image: containrrr/watchtower:latest
                    networks:
                      - isolated_30
                    volumes:
                      - /var/run/docker.sock:/var/run/docker.sock
          docker_containers:
            - name: linuxhq
              image: httpd:2.4
          docker_networks:
            - name: linuxhq
              driver: bridge
              enable_ipv6: false
              scope: local
          docker_systemd:
            - containerd.service
            - docker.service
            - docker-compose@watchtower.service
          docker_users:
            - linuxhq

## License

Copyright (C) 2021 Taylor Kimball <tkimball@linuxhq.org>

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program. If not, see <http://www.gnu.org/licenses/>.
