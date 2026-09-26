@Library('codigo-shared-library') _

// nodePipelineV2(
//     nodeImage: 'node:22-alpine',
//     installCommand: 'npm ci',
//     testCommand: 'npm test',
// )

// nodePipelineV3(
//     appName: 'sonar-api-node',
//     nodeImage: 'node:22-alpine',
//     installCommand: 'npm ci',
//     testCommand: 'npm test',
//     lintCommand: 'npm run lint',
//     runLint: false
// )

//closures anidados

nodePipelineClosure {
    docker {
        image = 'node:24-alpine'
    }

    install {
        command = 'npm ci'
    }

    test {
        command = 'npm test'
    }

    lint {
        enabled = true
        command = 'npm run lint'
    }
}