
## Troubleshooting Telefonie für einen Standort
- Sind Agenten von einem bestimmten Standort an einem WebRTC Kamailio angemeldet:
  ```sql
select * from session_contacts where (contact_target like "%101" or contact_target like "%109") and external_ip like "172.20%";
```

- Seit wann sind die Agenten in einer Schicht:
   ```sql
  select dutystart from session_contacts
inner join acd_job on jobid = session_id
where (contact_target like "%101" or contact_target like "%109") and external_ip like "172.20%"; 
```
- Haben die Agenten Calls:
   ```sql
   select connect_date, callerid from session_contacts
inner join acd_job on jobid = session_id
inner join acd_activecalls on member_id = acd_activecalls.memberid
where (contact_target like "%101" or contact_target like "%109") and external_ip like "172.20%";
```
