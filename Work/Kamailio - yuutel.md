# Register at yuutel

- creating table for uac module:
```sql
CREATE TABLE `uacreg` (
    `id` INT(10) UNSIGNED AUTO_INCREMENT PRIMARY KEY NOT NULL,
    `l_uuid` VARCHAR(64) DEFAULT '' NOT NULL,
    `l_username` VARCHAR(64) DEFAULT '' NOT NULL,
    `l_domain` VARCHAR(64) DEFAULT '' NOT NULL,
    `r_username` VARCHAR(64) DEFAULT '' NOT NULL,
    `r_domain` VARCHAR(64) DEFAULT '' NOT NULL,
    `realm` VARCHAR(64) DEFAULT '' NOT NULL,
    `auth_username` VARCHAR(64) DEFAULT '' NOT NULL,
    `auth_password` VARCHAR(64) DEFAULT '' NOT NULL,
    `auth_ha1` VARCHAR(128) DEFAULT '' NOT NULL,
    `auth_proxy` VARCHAR(255) DEFAULT '' NOT NULL,
    `expires` INT DEFAULT 0 NOT NULL,
    `flags` INT DEFAULT 0 NOT NULL,
    `reg_delay` INT DEFAULT 0 NOT NULL,
    `contact_addr` VARCHAR(255) DEFAULT '' NOT NULL,
    `socket` VARCHAR(128) DEFAULT '' NOT NULL,
    CONSTRAINT l_uuid_idx UNIQUE (`l_uuid`)
);
INSERT INTO uacreg(realm, auth_username, auth_password, expires, contact_addr, socket) VALUES ('bt.siptel.at', 'embersmediashop', '', 90, '"devpubpk01.sabienzia.com:5061;transport=tls', 'tls:10.136.136.206:5061');

INSERT INTO `uacreg` (`l_uuid`, `l_username`, `l_domain`, `r_username`, `r_domain`, `realm`, `auth_username`, `auth_password`, `auth_ha1`, `auth_proxy`, `expires`, `flags`, `reg_delay`, `contact_addr`, `socket`)
VALUES
	('embersmediashop', 'embersmediashop', 'devpubpk01.sabienzia.com', 'embersmediashop', 'bt.siptel.at', 'bt.siptel.at', 'embersmediashop', 'mXl892d-jk', '', 'sip:bt.siptel.at:5061;transport=tls', 1800, 0, 0, 'devpubpk01.sabienzia.com:5061;transport=tls', 'tls:193.84.65.81:5061');


INSERT INTO version (table_name, table_version) values ('uacreg','5');
```

Temporäre Testnummer (In und Outbound): +43720704338  
Host: [bt.siptel.at](bt.siptel.at) – Restliche Angaben in den Dokumenten anbei.  
Username: embersmediashop  
Passwort: 