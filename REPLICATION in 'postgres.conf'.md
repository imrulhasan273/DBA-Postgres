

```conf
#------------------------------------------------------------------------------
# REPLICATION
#------------------------------------------------------------------------------

# - Sending Server(s) -

# Set these on the master and on any standby that will send replication data.

#max_wal_senders = 10		# max number of walsender processes
				# (change requires restart)
#wal_keep_segments = 0		# in logfile segments, 16MB each; 0 disables
#wal_sender_timeout = 60s	# in milliseconds; 0 disables

#max_replication_slots = 10	# max number of replication slots
				# (change requires restart)
#track_commit_timestamp = off	# collect timestamp of transaction commit
				# (change requires restart)

# - Master Server -

# These settings are ignored on a standby server.

#synchronous_standby_names = ''	# standby servers that provide sync rep
				# method to choose sync standbys, number of sync standbys,
				# and comma-separated list of application_name
				# from standby(s); '*' = all
#vacuum_defer_cleanup_age = 0	# number of xacts by which cleanup is delayed

# - Standby Servers -

# These settings are ignored on a master server.

#hot_standby = on			# "off" disallows queries during recovery
					# (change requires restart)
#max_standby_archive_delay = 30s	# max delay before canceling queries
					# when reading WAL from archive;
					# -1 allows indefinite delay
#max_standby_streaming_delay = 30s	# max delay before canceling queries
					# when reading streaming WAL;
					# -1 allows indefinite delay
#wal_receiver_status_interval = 10s	# send replies at least this often
					# 0 disables
#hot_standby_feedback = off		# send info from standby to prevent
					# query conflicts
#wal_receiver_timeout = 60s		# time that receiver waits for
					# communication from master
					# in milliseconds; 0 disables
#wal_retrieve_retry_interval = 5s	# time to wait before retrying to
					# retrieve WAL after a failed attempt

# - Subscribers -

# These settings are ignored on a publisher.

#max_logical_replication_workers = 4	# taken from max_worker_processes
					# (change requires restart)
#max_sync_workers_per_subscription = 2	# taken from max_logical_replication_workers
```

# **REPLICATION Segment in `postgres.conf`**

---

## **Sending Server(s)**

---

- Set these on the master and on any standby that will send replication data.

---

#max_wal_senders = 10		# max number of walsender processes
				# (change requires restart)
#wal_keep_segments = 0		# in logfile segments, 16MB each; 0 disables
#wal_sender_timeout = 60s	# in milliseconds; 0 disables

#max_replication_slots = 10	# max number of replication slots
				# (change requires restart)
#track_commit_timestamp = off	# collect timestamp of transaction commit
				# (change requires restart)

## **Master Server**

- These settings are ignored on a standby server.

#synchronous_standby_names = ''	# standby servers that provide sync rep
				# method to choose sync standbys, number of sync standbys,
				# and comma-separated list of application_name
				# from standby(s); '*' = all
#vacuum_defer_cleanup_age = 0	# number of xacts by which cleanup is delayed

## **Standby Servers**

- These settings are ignored on a master server.

#hot_standby = on			# "off" disallows queries during recovery
					# (change requires restart)
#max_standby_archive_delay = 30s	# max delay before canceling queries
					# when reading WAL from archive;
					# -1 allows indefinite delay
#max_standby_streaming_delay = 30s	# max delay before canceling queries
					# when reading streaming WAL;
					# -1 allows indefinite delay
#wal_receiver_status_interval = 10s	# send replies at least this often
					# 0 disables
#hot_standby_feedback = off		# send info from standby to prevent
					# query conflicts
#wal_receiver_timeout = 60s		# time that receiver waits for
					# communication from master
					# in milliseconds; 0 disables
#wal_retrieve_retry_interval = 5s	# time to wait before retrying to
					# retrieve WAL after a failed attempt

## **Subscribers**

- These settings are ignored on a publisher.

#max_logical_replication_workers = 4	# taken from max_worker_processes
					# (change requires restart)
#max_sync_workers_per_subscription = 2	# taken from max_logical_replication_workers

---

---














