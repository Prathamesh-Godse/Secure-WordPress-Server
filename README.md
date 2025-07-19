# Secure WordPress Server

> A comprehensive guide to deploying, hardening, and optimizing a WordPress server with SSH security, firewall rules, NGINX tuning, PHP/MariaDB hardening, and advanced caching.

You can check the website [here](https://prathameshgodse.com)

## Table of Contents

### [1. SSH Setup & Maintenance for Remote Devices](Documentation/1.md)
### [2. SSH Access Management & Configuration](Documentation/2.md)
### [3. SSH Key-Based Authentication & Disabling Password Login](Documentation/3.md)
### [4. SSH Host Alias & Server Maintenance](Documentation/4.md)
### [5. Firewall Configuration: UFW + VPS Inbound Rules](Documentation/5.md)
### [6. Fail2Ban SSH Brute-Force Protection](Documentation/6.md)
### [7. Setting Server Time Zone with timedatectl](Documentation/7.md)
### [8. Creating and Optimizing Swap on a Linux Server](Documentation/8.md)
### [9. Harden Shared Memory](Documentation/9.md)
### [10. Disable IPv6 on the Server](Documentation/10.md)
### [11. Harden and Optimize Network Layer](Documentation/11.md)
### [12. TCP Congestion Control](Documentation/12.md)
### [13. File Access Time Optimization](Documentation/13.md)
### [14. Increase Open File Limits](Documentation/14.md)
### [15. Domain Name Setup](Documentation/15.md)
### [16. Installing and Verifying Nginx Web Server](Documentation/16.md)
### [17. Install MariaDB and PHP 8.3 + Extensions](Documentation/17.md)
### [18. Troubleshooting Errors in Nginx Configuration](Documentation/18.md)
### [19. Nginx Optimization and Modular Configuration](Documentation/19.md)
### [20. basic_settings.conf](Documentation/20.md)
### [21. buffers.conf](Documentation/21.md)
### [22. timeouts.conf](Documentation/22.md)
### [23. gzip.conf](Documentation/23.md)
### [24. brotli.conf](Documentation/24.md)
### [25. file_handle_cache.conf](Documentation/25.md)
### [26. HTTP Block](Documentation/26.md)
### [27. Too Many Open Files: Nginx File Descriptor Limit](Documentation/27.md)
### [28. Bash Aliases for Nginx & PHP Management](Documentation/28.md)
### [29. Securing MariaDB (MySQL) Server](Documentation/29.md)
### [30. MariaDB Configuration & Optimization](Documentation/30.md)
### [31. MariaDB Optimization & Binary Log Management](Documentation/31.md)
### [32. MariaDB Performance Tuning with MySQLTuner & File Limits](Documentation/32.md)
### [33. PHP Hardening with Custom server_override.ini](Documentation/33.md)
### [34. PHP Hardening & Optimization](Documentation/34.md)
### [35. PHP-FPM: Increase File Descriptor and Core Dump Limits](Documentation/35.md)
### [36. Create Web Root Directory for Your Domain](Documentation/36.md)
### [37. Nginx Server Block for example.com](Documentation/37.md)
### [38. Configure Browser Caching in Nginx](Documentation/38.md)
### [39. Nginx FastCGI Optimization Configuration](Documentation/39.md)
### [40. Nginx Server Block: example.com Configuration](Documentation/40.md)
### [41. Enabling the Nginx Site Configuration](Documentation/41.md)
### [42. Database & Admin Credential Setup for WordPress](Documentation/42.md)
### [43. Download and Prepare WordPress](Documentation/43.md)
### [44. Configure wp-config.php](Documentation/44.md)
### [45. WordPress Deployment Steps Explained](Documentation/45.md)
### [46. Log File Inspection for NGINX](Documentation/46.md)
### [47. User & Group Management](Documentation/47.md)
### [48. User & Group Management and PHP-FPM Configuration](Documentation/48.md)
### [49. Isolated PHP-FPM Pool for example.com with Custom Logging](Documentation/49.md)
### [50. Secure File & Directory Permissions for example.com](Documentation/50.md)
### [51. Disable Dangerous PHP Functions for example.com](Documentation/51.md)
### [52. Securing File Permissions in /var/www/example.com/public_html/](Documentation/52.md)
### [53. Secure Custom Temporary Directory for PHP](Documentation/53.md)
### [54. Obtain SSL Certificates](Documentation/54.md)
### [55. Create SSL Include File for Nginx](Documentation/55.md)
### [56. /etc/nginx/ssl/ssl_all_sites.conf – Explanation & Breakdown](Documentation/56.md)
### [57. example.com.conf — Secure Nginx Server Block](Documentation/57.md)
### [58. Check Domain Response Headers](Documentation/58.md)
### [59. Enabling Secure HTTP Headers in Nginx for example.com](Documentation/59.md)
### [60. Enabling Security Headers for example.com via Nginx](Documentation/60.md)
### [61. Optimizing Static Asset Caching for example.com](Documentation/61.md)
### [62. Nginx WordPress Hardening: nginx_security_directives.conf](Documentation/62.md)
### [63. Integrating Security Rules & Verifying Blocked Access](Documentation/63.md)
### [64. Temporary PHP Plugin Access Test — And Revert Instructions](Documentation/64.md)
### [65. Nginx Rate Limiting for WordPress Security](Documentation/65.md)
### [66. Activating NinjaFirewall with Full WAF Mode](Documentation/66.md)
### [67. Restricting MySQL Privileges & Disabling REST API in WordPress](Documentation/67.md)
### [68. What is PageSpeed Insights?](Documentation/68.md)
### [69. Disable Post Revisions in WordPress](Documentation/69.md)
### [70. WordPress & PHP Memory Configuration Update](Documentation/70.md)
### [71. WordPress Cron Optimization Using System Cron](Documentation/71.md)
### [72. Enable OPCache in PHP-FPM for example.com](Documentation/72.md)
### [73. Checking PHP File Count for OPCache Configuration](Documentation/73.md)
### [74. Enabling FastCGI Caching in NGINX for Performance Optimization](Documentation/74.md)
### [75. Enabling FastCGI Caching in NGINX Site Configuration](Documentation/75.md)
### [76. Configuring Cache Exclusions in NGINX](Documentation/76.md)
### [77. Final Integration of FastCGI Cache Exclusion and Headers in NGINX](Documentation/77.md)
### [78. Removing FastCGI Caching from NGINX Configuration](Documentation/78.md)
### [79. NGINX Configuration for WP Super Cache Exclusions](Documentation/79.md)
### [80. Integrating WP Super Cache NGINX Rules into Site Configuration](Documentation/80.md)
### [81. Temporary Setup and Revert for WP Super Cache](Documentation/81.md)
### [82. W3 Total Cache](Documentation/82.md)
### [83. Setting Up Redis Server for WordPress Object Caching](Documentation/83.md)
### [84. Integrating WP Super Cache & Redis Object Cache with WooCommerce Support](Documentation/84.md)
### [85. PHP-FPM Pool User Monitoring & On-Demand Configuration](Documentation/85.md)
### [86. Restore Real Visitor IP Behind Cloudflare + Cloudflare Optimization](Documentation/86.md)

## About

> This project sets up a production-grade WordPress server from scratch on a Ubuntu Server VPS, focusing on strong security, performance optimization, and system-level hardening. It walks through SSH configuration, firewall setup, brute-force protection, secure NGINX and PHP tuning, MariaDB optimization, SSL integration, and advanced caching — all documented step-by-step.

## features 

- Security & Hardening
  - SSH key-based authentication (password login disabled)
  - Fail2Ban to prevent SSH brute-force attacks
  - UFW firewall with strict inbound rules
  - Hardened shared memory and disabled IPv6
  - PHP hardening (disabled dangerous functions, isolated pools)
  - Secure file and directory permissions for WordPress
  - Security headers and rate limiting via NGINX
  - MariaDB hardening and limited SQL privileges
  - NinjaFirewall integration with full WAF mode
- Server Optimization
  - TCP congestion control tuning
  - Optimized swap and file access settings
  - Increased file descriptor limits
  - Network stack hardening
  - System timezone setup
- Web Server Configuration
  - NGINX modular configuration with reusable includes
  - Custom server blocks for multiple domains
  - SSL certificate setup and reuse (ssl_all_sites.conf)
  - Gzip and Brotli compression
  - Real IP restoration behind Cloudflare
- Database & PHP Tuning
  - MariaDB optimization with MySQLTuner
  - Binary log management and performance tuning
  - PHP-FPM pool customization and monitoring
  - OPCache configuration for PHP acceleration
- WordPress Deployment
  - Step-by-step WordPress installation
  - wp-config.php hardening
  - Disable REST API and post revisions
  - Use of system cron for better scheduling
  - Redis-based object caching with WooCommerce support
  - FastCGI & WP Super Cache integration and management
