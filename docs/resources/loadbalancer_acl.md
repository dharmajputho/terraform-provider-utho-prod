---
page_title: "Utho: utho_loadbalancer_acl"
subcategory: "Load Balancing"
description: |-
  Add and manage ACL rules on a Utho Load Balancer frontend.
---

# utho_loadbalancer_acl

Adds an ACL (Access Control List) rule to a Utho Load Balancer frontend. ACL rules allow you to route or filter traffic based on request attributes like URL path, HTTP method, user agent, or host header.

## Example Usage

### URL path based routing

```hcl
resource "utho_loadbalancer_acl" "api_route" {
  loadbalancer_id = utho_loadbalancer.main.id
  frontend_id     = utho_loadbalancer_frontend.http.id
  name            = "api-route"
  condition_type  = "url_path"
  value           = "{\"type\":\"url_path\",\"data\":[\"/api\"],\"frontend_id\":\"${utho_loadbalancer_frontend.http.id}\"}"
}
```

### HTTP method filter

```hcl
resource "utho_loadbalancer_acl" "post_only" {
  loadbalancer_id = utho_loadbalancer.main.id
  frontend_id     = utho_loadbalancer_frontend.http.id
  name            = "post-only"
  condition_type  = "http_method"
  value           = "{\"type\":\"http_method\",\"data\":\"POST\",\"frontend_id\":\"${utho_loadbalancer_frontend.http.id}\"}"
}
```

### User agent filter

```hcl
resource "utho_loadbalancer_acl" "block_bot" {
  loadbalancer_id = utho_loadbalancer.main.id
  frontend_id     = utho_loadbalancer_frontend.http.id
  name            = "block-googlebot"
  condition_type  = "http_user_agent"
  value           = "{\"type\":\"http_user_agent\",\"data\":\"GoogleBot\",\"frontend_id\":\"${utho_loadbalancer_frontend.http.id}\"}"
}
```

## Argument Reference

| Argument          | Type   | Required | Description |
|-------------------|--------|----------|-------------|
| `loadbalancer_id` | String | Yes      | Load balancer ID. Changing this forces a new resource. |
| `frontend_id`     | String | Yes      | Frontend ID to attach this ACL rule to. |
| `name`            | String | Yes      | ACL rule name. |
| `condition_type`  | String | Yes      | Condition type. See [Condition Types](#condition-types) below. |
| `value`           | String | Yes      | ACL rule value as a JSON string. |

### Condition Types

| Value              | Description |
|--------------------|-------------|
| `url_path`         | Match on URL path prefix. |
| `url_path_regex`   | Match on URL path using a regex pattern. |
| `http_method`      | Match on HTTP method (GET, POST, PUT, DELETE, etc.). |
| `http_user_agent`  | Match on User-Agent header. |
| `http_referer`     | Match on Referer header. |
| `host`             | Match on Host header. |

## Attribute Reference

| Attribute | Type   | Description |
|-----------|--------|-------------|
| `id`      | String | Unique ACL rule ID. |
