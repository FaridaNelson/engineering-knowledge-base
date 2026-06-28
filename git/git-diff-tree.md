# git diff-tree

Displays differences introduced by a commit.

Example:

git diff-tree COMMIT_HASH

---

## -r option

-r means recursive traversal.

Git traverses subdirectories and shows all changed files.

Example:

repo/
├── frontend/
│ └── App.jsx
└── backend/
└── server.js

git diff-tree -r

Output:

frontend/App.jsx
backend/server.js
