---
{"dg-publish":true,"permalink":"/ingest/sin/tools-index/","tags":["Tools","Capabilities","gardenEntry"]}
---

## Comprehensive Tool Server Documentation

This document provides a complete reference for all available tool servers and their methods.

### **1. HubSpot MCP Server (`hubspot-mcp-server`)**
- **Description:** A server for interacting with the HubSpot CRM API.
- **Base URL:** `http://localhost:8000/hubspot`
- **Methods:**
  - `tool_hubspot_get_user_details_post`: Authenticates the HubSpot access token and retrieves the user's permissions, account details, User ID, and Hub ID. This should be used first to understand context and permissions.
  - `tool_hubspot_list_objects_post`: Retrieves a paginated list of objects (e.g., contacts, deals) of a specified type.
  - `tool_hubspot_search_objects_post`: Performs advanced, filtered searches across HubSpot objects using complex criteria.
  - `tool_hubspot_batch_create_objects_post`: Creates multiple HubSpot objects of the same type in a single bulk operation.
  - `tool_hubspot_batch_update_objects_post`: Updates multiple existing HubSpot objects of the same type in a single bulk operation.
  - `tool_hubspot_batch_read_objects_post`: Retrieves multiple HubSpot objects by their unique IDs.
  - `tool_hubspot_create_association_post`: Creates a link or relationship between two HubSpot objects (e.g., associating a contact with a company).
  - `tool_hubspot_get_association_definitions_post`: Retrieves the valid types of associations that can be created between two object types.
  - `tool_hubspot_list_associations_post`: Lists all existing relationships for a specific object.
  - `tool_hubspot_list_properties_post`: Retrieves a complete catalog of all available properties for a given object type.
  - `tool_hubspot_get_property_post`: Retrieves detailed information about a single, specific property.
  - `tool_hubspot_create_property_post`: Creates a new custom property for a HubSpot object type.
  - `tool_hubspot_update_property_post`: Updates the settings of an existing custom property.
  - `tool_hubspot_create_engagement_post`: Creates a new engagement (like a Note or Task) and associates it with CRM records.
  - `tool_hubspot_get_engagement_post`: Retrieves a single engagement by its ID.
  - `tool_hubspot_update_engagement_post`: Updates an existing engagement's content or details.
  - `tool_hubspot_generate_feedback_link_post`: Generates a link for users to submit feedback on the HubSpot tool's performance.
  - `tool_hubspot_get_schemas_post`: Retrieves all custom object schemas defined in the HubSpot account.
  - `tool_hubspot_get_link_post`: Generates a direct URL to a HubSpot UI page for a specific object or list.
  - `tool_hubspot_list_workflows_post`: Retrieves a paginated list of all automation workflows in the HubSpot account.
  - `tool_hubspot_get_workflow_post`: Retrieves detailed information and actions for a specific workflow by its ID.

---

### **2. Sequential Thinking Server (`sequential-thinking-server`)**
- **Description:** A dynamic tool for breaking down complex problems into a logical sequence of thoughts.
- **Base URL:** `http://localhost:8000/thinking`
- **Methods:**
  - `tool_sequentialthinking_post`: Analyzes problems through an adaptive, multi-step thinking process. It allows for planning, revision, hypothesis generation, and verification to arrive at a solution.

---

### **3. Time MCP Server (`mcp-time`)**
- **Description:** A server for handling time-related queries.
- **Base URL:** `http://localhost:8000/time`
- **Methods:**
  - `tool_get_current_time_post`: Fetches the current time in a specified timezone.
  - `tool_convert_time_post`: Converts a time from one timezone to another.

---

### **4. Todoist MCP Server (`todoist-mcp-server`)**
- **Description:** A server for managing tasks in Todoist.
- **Base URL:** `http://localhost:8000/todoist`
- **Methods:**
  - `tool_todoist_create_task_post`: Creates a new task with an optional description, due date, and priority.
  - `tool_todoist_get_tasks_post`: Retrieves a list of tasks, with support for various filters.
  - `tool_todoist_update_task_post`: Updates an existing task's properties.
  - `tool_todoist_delete_task_post`: Deletes a task.
  - `tool_todoist_complete_task_post`: Marks a task as complete.

---

### **5. Shell MCP Server (`mcp-shell-server`)**
- **Description:** A server for executing safe, sandboxed shell commands.
- **Base URL:** `http://localhost:8000/shell`
- **Methods:**
  - `tool_shell_execute_post`: Executes an allowed shell command (e.g., `pwd`, `ls`, `grep`, `cat`, `wc`, `touch`, `find`).

---

### **6. Filesystem MCP Server (`secure-filesystem-server`)**
- **Description:** A server for securely interacting with a local filesystem within allowed directories.
- **Base URL:** `http://localhost:8000/filesystem`
- **Methods:**
  - `tool_read_file_post`: Reads the complete contents of a single file.
  - `tool_read_multiple_files_post`: Reads the contents of multiple files in one operation.
  - `tool_write_file_post`: Creates a new file or completely overwrites an existing file.
  - `tool_edit_file_post`: Performs line-based edits on a text file and returns a diff of the changes.
  - `tool_create_directory_post`: Creates a new directory, including any necessary parent directories.
  - `tool_list_directory_post`: Lists all files and subdirectories within a specified path.
  - `tool_directory_tree_post`: Returns a recursive JSON tree view of a directory's contents.
  - `tool_move_file_post`: Moves or renames a file or directory.
  - `tool_search_files_post`: Recursively searches for files and directories matching a pattern.
  - `tool_get_file_info_post`: Retrieves detailed metadata (size, modification date, etc.) for a file or directory.
  - `tool_list_allowed_directories_post`: Returns the list of directories that the server is permitted to access.

---

### **7. Fetch MCP Server (`mcp-fetch`)**
- **Description:** A server that provides internet access to fetch web content.
- **Base URL:** `http://localhost:8000/fetch`
- **Methods:**
  - `tool_fetch_post`: Fetches the content of a given URL and can optionally extract it into clean markdown.

---

### **8. SQLite MCP Server (`sqlite`)**
- **Description:** A server for interacting with a SQLite database.
- **Base URL:** `http://localhost:8000/sqlite`
- **Methods:**
  - `tool_read_query_post`: Executes a `SELECT` query on the database.
  - `tool_write_query_post`: Executes an `INSERT`, `UPDATE`, or `DELETE` query.
  - `tool_create_table_post`: Creates a new table in the database.
  - `tool_list_tables_post`: Lists all tables in the database.
  - `tool_describe_table_post`: Retrieves the schema information for a specific table.
  - `tool_append_insight_post`: Adds a business insight or note to an internal memo.

---

### **9. Slack MCP Server (`Slack MCP Server`)**
- **Description:** A server for interacting with the Slack API.
- **Base URL:** `http://localhost:8000/slack`
- **Methods:**
  - `tool_slack_list_channels_post`: Lists public channels in the workspace.
  - `tool_slack_post_message_post`: Posts a new message to a specified channel.
  - `tool_slack_reply_to_thread_post`: Posts a reply within a specific message thread.
  - `tool_slack_add_reaction_post`: Adds an emoji reaction to a message.
  - `tool_slack_get_channel_history_post`: Retrieves recent message history from a channel.
  - `tool_slack_get_thread_replies_post`: Retrieves all replies within a message thread.
  - `tool_slack_get_users_post`: Retrieves a list of all users in the workspace.
  - `tool_slack_get_user_profile_post`: Retrieves detailed profile information for a specific user.

---

### **10. Memory MCP Server (`memory-server`)**
- **Description:** A server for managing a knowledge graph of entities and their relationships.
- **Base URL:** `http://localhost:8000/memory`
- **Methods:**
  - `tool_create_entities_post`: Creates one or more new entities (nodes) in the knowledge graph.
  - `tool_create_relations_post`: Creates relationships (edges) between existing entities.
  - `tool_add_observations_post`: Adds factual observations or properties to existing entities.
  - `tool_delete_entities_post`: Deletes entities and their associated relations from the graph.
  - `tool_delete_observations_post`: Deletes specific observations from an entity.
  - `tool_delete_relations_post`: Deletes specific relationships between entities.
  - `tool_read_graph_post`: Reads the entire knowledge graph.
  - `tool_search_nodes_post`: Searches for nodes in the graph based on a query.
  - `tool_open_nodes_post`: Retrieves specific nodes by their names.

---

### **11. Docker MCP Server (`docker-mcp`)**
- **Description:** A server for managing Docker containers and services.
- **Base URL:** `http://localhost:8000/docker`
- **Methods:**
  - `tool_create_container_post`: Creates a new standalone Docker container from an image.
  - `tool_deploy_compose_post`: Deploys a new service stack from a Docker Compose file.
  - `tool_get_logs_post`: Retrieves the latest logs for a specified container.
  - `tool_list_containers_post`: Lists all running and stopped Docker containers.

---

### **12. WordPress Content Manager**
- **Description:** A tool for managing WordPress site content (posts, pages, media, users, etc.) via the REST API.
- **Configuration:** Server URL and credentials are set via internal configuration.
- **Methods:**
  - **Posts & Pages:** `list_posts`, `get_post`, `create_post`, `update_post`, `delete_post`, `list_pages`, `get_page`, `create_page`, `update_page`, `delete_page`.
  - **Media:** `list_media`, `get_media`, `upload_media` (from URL), `delete_media`.
  - **Taxonomies:** `list_categories`, `create_category`, `delete_category`, `list_tags`, `create_tag`, `delete_tag`.
  - **Users:** `list_users`, `get_user`, `create_user`, `delete_user`, `get_current_user`.
  - **Comments:** `list_comments`, `get_comment`, `create_comment`, `delete_comment`.
  - **Settings:** `get_settings`, `update_settings`.

---

### **13. Plesk Management**
- **Description:** A tool for managing Plesk web hosting servers via the REST API.
- **Configuration:** Server URL and credentials are set via internal configuration.
- **Methods:**
  - **Server & Auth:** `get_server_info`, `get_server_ips`, `generate_secret_key`, `install_license`.
  - **Domains:** `list_domains`, `create_domain`, `get_domain_details`, `update_domain_status`, `delete_domain`.
  - **Clients:** `list_clients`, `create_client`, `get_client_details`, `suspend_client`, `delete_client`.
  - **Databases:** `list_databases`, `create_database`, `delete_database`, `create_database_user`, `delete_database_user`.
  - **DNS & FTP:** `list_dns_records`, `create_dns_record`, `list_ftp_users`, `create_ftp_user`.
  - **Extensions & CLI:** `list_extensions`, `install_extension`, `execute_cli_command`.