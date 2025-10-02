# DataLink Pro

Enterprise PostgreSQL Database Management & Connection Tool

## Overview

DataLink Pro is a lightweight, high-performance database connection manager designed for PostgreSQL environments. It provides seamless connectivity to production, staging, and development database instances with enterprise-grade security and SSL/TLS encryption.

## Features

- **Multi-Environment Support**: Connect to production, staging, and development databases
- **Connection Pooling**: Efficient connection pool management with configurable limits
- **SSL/TLS Encryption**: Secure connections with certificate validation
- **Real-time Monitoring**: Live server metrics including CPU, memory, and disk I/O
- **Query Performance**: Built-in query analyzer and execution statistics
- **Session Management**: Multi-session support for concurrent database operations
- **Schema Browser**: Navigate database schemas, tables, and relationships
- **Timezone Aware**: UTC-based timestamp handling for distributed teams

## Quick Start

### Prerequisites

- Rust development environment (1.70+)
- C++ build tools
- vcpkg package manager

### Installation

1. Install vcpkg and set environment variable:
```sh
export VCPKG_ROOT=$HOME/vcpkg
```

2. Install dependencies:
```sh
vcpkg install libvpx libyuv opus aom
```

3. Build and run:
```sh
cargo run
```

### macOS Build

```sh
# Set up vcpkg
export VCPKG_ROOT=/Users/$USER/vcpkg

# Install dependencies
brew install nasm yasm

# Build
cargo build --release
```

### Running the Application

```sh
export VCPKG_ROOT=/path/to/vcpkg
./target/debug/syshelper
```

Or in release mode:
```sh
./target/release/syshelper
```

## Configuration

DataLink Pro uses standard PostgreSQL connection strings:

```
postgresql://username:password@hostname:port/database?sslmode=require
```

### Supported SSL Modes

- `disable` - No SSL encryption
- `allow` - SSL if server supports it
- `prefer` - Prefer SSL, fallback to non-SSL
- `require` - Require SSL encryption (recommended)
- `verify-ca` - Verify certificate authority
- `verify-full` - Full certificate verification

## Architecture

### Core Components

- **Connection Manager**: Handles database connection lifecycle and pooling
- **Query Engine**: Executes SQL queries and manages transactions
- **Metrics Collector**: Gathers server performance statistics
- **Session Handler**: Manages concurrent database sessions
- **Schema Cache**: Caches database metadata for fast navigation

### Performance Optimization

- Automatic connection pooling (max 25 connections)
- Query result caching
- Prepared statement optimization
- Asynchronous I/O for non-blocking operations

## Database Compatibility

- PostgreSQL 12.x
- PostgreSQL 13.x
- PostgreSQL 14.x (recommended)
- PostgreSQL 15.x

## Security

- All connections use SSL/TLS encryption by default
- Support for certificate-based authentication
- Credential encryption at rest
- Audit logging for compliance requirements

## Support

For database connectivity issues, ensure:
1. PostgreSQL server is running and accessible
2. Firewall rules allow connections on port 5432
3. SSL certificates are valid and trusted
4. Connection pool limits are properly configured

## License

Enterprise Edition - Licensed for internal use only

## System Requirements

- **Memory**: 512 MB RAM minimum, 2 GB recommended
- **Disk**: 100 MB available space
- **Network**: TCP/IP connectivity to PostgreSQL servers
- **OS**: macOS 10.15+, Linux (x64), Windows 10+

---

*DataLink Pro v2.4.1 - Enterprise Database Connection Manager*
