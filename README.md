# RBAC---K8S

Kubernetes Role-Based Access Control (RBAC) allows you to control who can access resources within your Kubernetes cluster and what actions they can perform. RBAC works by assigning specific roles (permissions) to users or groups, and it's enforced by the Kubernetes API server.

### Key Concepts of RBAC

1. **Role**: A Role defines a set of permissions (rules) within a namespace. Permissions specify which resources can be accessed and what actions (like `get`, `list`, `create`, `delete`) can be performed.
   
2. **ClusterRole**: Similar to Role, but is applied cluster-wide, not limited to a single namespace. ClusterRoles can be used in both namespace-specific and cluster-wide scopes.

3. **RoleBinding**: Binds a Role to a user or group in a specific namespace, granting the permissions defined in the Role.

4. **ClusterRoleBinding**: Binds a ClusterRole to users or groups at the cluster level, granting cluster-wide permissions.

### Example of Kubernetes RBAC

#### 1. Create a Role
Here’s an example of a `Role` that allows users to `get`, `list`, and `watch` pods in the `development` namespace.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: development
  name: pod-reader
rules:
- apiGroups: [""]   # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

#### 2. Create a RoleBinding
Once the `Role` is created, we can create a `RoleBinding` to bind this `Role` to a user (or group).

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods-binding
  namespace: development
subjects:
- kind: User
  name: john  # The user who is being granted access
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader  # The Role being referenced
  apiGroup: rbac.authorization.k8s.io
```

This `RoleBinding` grants the user `john` the permissions defined in the `pod-reader` role to get, list, and watch pods in the `development` namespace.

#### 3. Create a ClusterRole
A `ClusterRole` can apply to all namespaces and can include permissions to non-namespaced resources (e.g., nodes, persistent volumes). Here’s an example that gives access to read all pods across the cluster.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

#### 4. Create a ClusterRoleBinding
You can use a `ClusterRoleBinding` to grant cluster-wide access. Here’s how to bind the `cluster-pod-reader` `ClusterRole` to a user.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-read-pods-binding
subjects:
- kind: User
  name: jane  # The user being granted cluster-wide access
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-pod-reader
  apiGroup: rbac.authorization.k8s.io
```

This `ClusterRoleBinding` grants the user `jane` read access to all pods in all namespaces across the cluster.

### Summary of the Workflow:
1. **Define roles** (Role or ClusterRole) specifying what actions can be performed on specific resources.
2. **Bind roles** to users (or groups) via RoleBinding (namespace-specific) or ClusterRoleBinding (cluster-wide).

This way, Kubernetes RBAC helps control who can interact with the cluster's resources, ensuring secure access management.
