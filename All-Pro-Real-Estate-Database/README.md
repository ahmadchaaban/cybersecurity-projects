# All-Pro Real Estate – Relational Database 
 Data Design & Implementation • **Focus:** ERD, constraints, data integrity  
<Summer/2025>

Overview
Normalized schema for a real-estate scenario (clients, properties, agents, transactions). ERD in MySQL Workbench; prototype in MS Access.

Objectives
- PK/FK relationships and referential integrity
- Example reporting queries (joins/aggregations)
- Optional audit fields (created_at/updated_at)

Files
- `ERD.mwb` (MySQL Workbench model)  
- `Database.accdb` (Access implementation)  
- `ERD.png` (exported diagram)  

Examnple Queries

-- Listings by agent
SELECT a.agent_id, a.name, COUNT(l.listing_id) AS total_listings
FROM agents a LEFT JOIN listings l ON l.agent_id = a.agent_id
GROUP BY a.agent_id, a.name;
