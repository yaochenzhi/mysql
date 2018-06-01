CREATE DATABASE test_db CHARSET utf8 COLLATE utf8_bin;

USE test_db;

CREATE TABLE test_tb (
    id int NOT NULL auto_increment,
    hostname varchar(20),
    ip_addr varchar(20),
    active tinyint(1) NOT NULL DEFAULT 1,
    PRIMARY KEY (id),
    UNIQUE KEY (ip_addr),
    );

INSERT INTO test_db (hostname, ip_addr) VALUES ("localhost", "127.0.0.1");

LOAD DATA LOCAL INFILE "/file/path" INTO TABLE test_db LINES TERMINATED BY "\n" FIELDS TERMINATED BY " " (@hn, @ip) SET hostname = @hn, ip_addr = @ip;

GRANT SELECT ON test_db.* TO "oneselect"@"%" IDENTIFIED BY "onepasswd";

FLUSH PRIVILEGES;


# FIND OUT SAME HOSTNAME
SELECT hostname, count(*) AS c FROM test_tb GROUP BY hostname HAVING c > 1;

# -> WITH MORE DETAILS
SELECT hostname, ip_addr FROM test_tb WHERE hostname in (SELECT hostname FROM (SELECT hostname, count(*) AS c FROM test_tb GROUP BY hostname HAVING c > 1) AS t1 );

# -> SUM NUM
SELECT SUM(c) FROM (SELECT hostname, count(*) AS c FROM test_tb GROUP BY hostname HAVING c > 1) AS t1;