pipeline {
    agent { label 'agent' }

    parameters {
        choice(
            name: 'ENVIRONMENT',
            choices: ['dev', 'qa', 'prod'],
            description: 'Select environment to deploy'
        )
    }

    environment {
        // mapping env → branch
        BRANCH_NAME = "${params.ENVIRONMENT == 'prod' ? 'master' : params.ENVIRONMENT}"
    }

    stages {

        stage('Clone Repository') {
            steps {
                echo "📌 Cloning branch: ${BRANCH_NAME}"
                git branch: "${BRANCH_NAME}", credentialsId: 'github', url: 'https://github.com/srinivasulu2004/frontend-vite-and-backend-java-.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    echo "⚙️ Cleaning old images"
                    docker rmi -f $(docker images -aq) || true
                    echo "🛠️ Building fresh Docker images"
                    docker compose build
                '''
            }
        }

        stage('Deployment of Application') {
            steps {
                sh '''
                    echo "🔥 Removing running containers"
                    docker rm -f $(docker ps -aq) || true
                    docker volume rm -f $(docker volume ls -q) || true

                    echo "🚀 Deploying new containers"
                    docker compose up -d
                '''
            }
        }

        stage('List Containers') {
            steps {
                sh 'docker ps -a'
            }
        }
    }

    post {
        success {
            mail to: 'srininivasulubehara@gmail.com',
                 subject: "Pipeline SUCCESS for ${params.ENVIRONMENT}",
                 body: "✔️ Pipeline executed successfully for branch: ${BRANCH_NAME}."
        }

        failure {
            mail to: 'srininivasulubehara@gmail.com',
                 subject: "Pipeline FAILED for ${params.ENVIRONMENT}",
                 body: "❌ Pipeline failed for branch: ${BRANCH_NAME}."
        }
    }
}

===================================================================================================================================================================================================
    Absolutely — I’ll explain exactly how the environment-based branch selection works and how Jenkins interprets it, step by step, in very simple and clear terms.

---

# 🧩 Step-by-step Explanation

---

## **1️⃣ Jenkins Parameter — choosing environment**

```groovy
parameters {
    choice(
        name: 'ENVIRONMENT',
        choices: ['dev', 'qa', 'prod'],
        description: 'Select environment to deploy'
    )
}
```

### What this does:

* When you click **Build with Parameters**, Jenkins shows a drop-down list.
* You select: `dev`, `qa`, or `prod`.

Example user input during triggering:

```
ENVIRONMENT = qa
```

This value is stored in:

```
params.ENVIRONMENT
```

---

## **2️⃣ Mapping ENVIRONMENT → branch**

```groovy
environment {
    BRANCH_NAME = "${params.ENVIRONMENT == 'prod' ? 'master' : params.ENVIRONMENT}"
}
```

### This line is the key logic.

Explanation:

* If ENVIRONMENT = prod
  → use `master` branch

* Otherwise
  → use the value itself

  * dev → dev
  * qa → qa

This is a conditional expression:

```
condition ? valueIfTrue : valueIfFalse
```

So:

| ENVIRONMENT | BRANCH_NAME |
| ----------- | ----------- |
| dev         | dev         |
| qa          | qa          |
| prod        | master      |

---

## **3️⃣ Passing branch to git clone**

```groovy
git branch: "${BRANCH_NAME}", credentialsId: 'github', url: 'https://github.com/....git'
```

Example:

* If QA pipeline is triggered:
  then

  ```
  BRANCH_NAME = "qa"
  ```

  so Jenkins runs:

  ```
  git clone --branch qa ...
  ```

* If production pipeline is triggered:

  ```
  BRANCH_NAME = "master"
  ```

  so Jenkins runs:

  ```
  git clone --branch master ...
  ```

---

## **4️⃣ Why we do this?**

### Because different environments require different code states:

| Branch        | Purpose                  |
| ------------- | ------------------------ |
| dev           | developer working branch |
| qa            | testing build            |
| master / prod | final production code    |

This avoids pushing half-finished code to prod.

---

## **5️⃣ Real Example**

👉 Suppose your repository has:

```
branches:
   master
   dev
   qa
```

When Jenkins runs:

### Scenario A — Developer testing:

User selects:

```
ENVIRONMENT = dev
```

Jenkins clones:

```
branch dev
```

### Scenario B — QA tester validation:

User selects:

```
ENVIRONMENT = qa
```

Jenkins clones:

```
branch qa
```

### Scenario C — Live production deployment:

User selects:

```
ENVIRONMENT = prod
```

Jenkins clones:

```
branch master
```

---

# ✔️ In one sentence:

**You choose an environment, Jenkins automatically selects the correct branch and clones it.**

---

# If you want next level

I can help you extend this with:

✔ environment-specific `.env` values
✔ deploying to different EC2 servers:

* dev → t2.micro
* qa → t3.small
* prod → t3.large

✔ deploying on different ports:

* dev → 3000
* qa → 4000
* prod → 80

✔ environment-specific Docker images
✔ environment-specific database URIs
✔ separate DNS records:

* dev.myapp.com
* qa.myapp.com
* myapp.com

---

If you want, tell me:

### What are the actual branch names?

Do you have:

```
dev
qa
master
```

or something else?

I’ll adjust the logic perfectly for your setup.

