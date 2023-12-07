Пытался отрефакторить hexlet-ci-app/.github/workflows/test-and-lint.yml таким образом:
```
name: test-and-lint

on: push

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3

      - name: Build project
        run: make setup

  test-lint:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Run linter
        run: make lint

      - name: Run tests
        run: make test
```

Но, несмтря на
```
  test-lint:
    runs-on: ubuntu-latest
    needs: build
```

происходила ошибка:
```
test-lint
failed Dec 7, 2023 in 0s
0s
Current runner version: '2.311.0'
Operating System
  Ubuntu
  22.04.3
  LTS
Runner Image
  Image: ubuntu-22.04
  Version: 20231205.1.0
  Included Software: https://github.com/actions/runner-images/blob/ubuntu22/20231205.1/images/ubuntu/Ubuntu2204-Readme.md
  Image Release: https://github.com/actions/runner-images/releases/tag/ubuntu22%2F20231205.1
Runner Image Provisioner
  2.0.321.1
GITHUB_TOKEN Permissions
  Actions: write
  Checks: write
  Contents: write
  Deployments: write
  Discussions: write
  Issues: write
  Metadata: read
  Packages: write
  Pages: write
  PullRequests: write
  RepositoryProjects: write
  SecurityEvents: write
  Statuses: write
Secret source: Actions
Prepare workflow directory
Prepare all required actions
Complete job name: test-lint
0s
Run make lint
  make lint
  shell: /usr/bin/bash -e {0}
make: *** No rule to make target 'lint'.  Stop.
Error: Process completed with exit code 2.
```

По-видимому, это связано с тем, что

[...Для обеспечения параллельности Github запускает задания в независимых директориях. Файлы, которые создает конкретное задание, не видно из других заданий. Если во время сборки build-backend у нас на диске оказались какие-то файлы, то задание test их не увидит. Для этого нужно дополнительно включать шаги по переносу данных из одного задания в другое...](https://ru.hexlet.io/courses/github-actions/lessons/jobs/theory_unit)
