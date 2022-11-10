## Task 1. Prepare the source database for migration

sudo apt install postgresql-13-pglogical

sudo su - postgres -c "gsutil cp gs://cloud-training/gsp918/pg_hba_append.conf ."
sudo su - postgres -c "gsutil cp gs://cloud-training/gsp918/postgresql_append.conf ."
sudo su - postgres -c "cat pg_hba_append.conf >> /etc/postgresql/13/main/pg_hba.conf"
sudo su - postgres -c "cat postgresql_append.conf >> /etc/postgresql/13/main/postgresql.conf"
sudo systemctl restart postgresql@13-main

sudo su - postgres
psql

\c postgres;
CREATE EXTENSION pglogical;
\c orders;
CREATE EXTENSION pglogical;

CREATE USER migration_admin PASSWORD 'DMS_1s_cool!';
ALTER DATABASE orders OWNER TO migration_admin;
ALTER ROLE migration_admin WITH REPLICATION;


\c postgres;
GRANT USAGE ON SCHEMA pglogical TO migration_admin;
GRANT ALL ON SCHEMA pglogical TO migration_admin;
GRANT SELECT ON pglogical.tables TO migration_admin;
GRANT SELECT ON pglogical.depend TO migration_admin;
GRANT SELECT ON pglogical.local_node TO migration_admin;
GRANT SELECT ON pglogical.local_sync_status TO migration_admin;
GRANT SELECT ON pglogical.node TO migration_admin;
GRANT SELECT ON pglogical.node_interface TO migration_admin;
GRANT SELECT ON pglogical.queue TO migration_admin;
GRANT SELECT ON pglogical.replication_set TO migration_admin;
GRANT SELECT ON pglogical.replication_set_seq TO migration_admin;
GRANT SELECT ON pglogical.replication_set_table TO migration_admin;
GRANT SELECT ON pglogical.sequence_state TO migration_admin;
GRANT SELECT ON pglogical.subscription TO migration_admin;

\c orders;
GRANT USAGE ON SCHEMA pglogical TO migration_admin;
GRANT ALL ON SCHEMA pglogical TO migration_admin;
GRANT SELECT ON pglogical.tables TO migration_admin;
GRANT SELECT ON pglogical.depend TO migration_admin;
GRANT SELECT ON pglogical.local_node TO migration_admin;
GRANT SELECT ON pglogical.local_sync_status TO migration_admin;
GRANT SELECT ON pglogical.node TO migration_admin;
GRANT SELECT ON pglogical.node_interface TO migration_admin;
GRANT SELECT ON pglogical.queue TO migration_admin;
GRANT SELECT ON pglogical.replication_set TO migration_admin;
GRANT SELECT ON pglogical.replication_set_seq TO migration_admin;
GRANT SELECT ON pglogical.replication_set_table TO migration_admin;
GRANT SELECT ON pglogical.sequence_state TO migration_admin;
GRANT SELECT ON pglogical.subscription TO migration_admin;

\c orders;
\dt
ALTER TABLE public.distribution_centers OWNER TO migration_admin;
ALTER TABLE public.inventory_items OWNER TO migration_admin;
ALTER TABLE public.order_items OWNER TO migration_admin;
ALTER TABLE public.products OWNER TO migration_admin;
ALTER TABLE public.users OWNER TO migration_admin;
\dt

ALTER TABLE public.inventory_items ADD PRIMARY KEY(id);
\q 
exit

## Task 2. Create a Database Migration Service connection profile for a stand-alone PostgreSQL database


## Task 3: Create networks and firewalls
 
gcloud compute networks create vpc-network-h049 --subnet-mode custom

gcloud compute networks subnets create subnet-a-iyha \
   --network vpc-network-h049 \
   --region us-east1 \
   --range 10.10.10.0/24

gcloud compute networks subnets create subnet-b-kpub \
   --network vpc-network-h049 \
   --region asia-south1 \
   --range 10.10.20.0/24

gcloud compute firewall-rules create rvnw-firewall-ssh \
--allow tcp:22 --network vpc-network-h049 --source-ranges 0.0.0.0/0 \
--priority 65535

gcloud compute firewall-rules create rbxs-firewall-rdp \
--allow tcp:3389 --network vpc-network-h049 --source-ranges 0.0.0.0/0 \
--priority 65535

gcloud compute firewall-rules create ixdz-firewall-icmp \
--allow icmp --network vpc-network-h049 --source-ranges 0.0.0.0/0 \
--priority 65535
