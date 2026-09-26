@Library('codigo-shared-library') _


// -> Shared Library de proyecto basico
// nodePipelineV1()

// -> Shared Library con configuraciones avanzadas (V2)
// nodePipelineV2(
//     nodeImage: 'node:22-alpine',
//     installCommand: 'npm ci',
//     testCommand: 'npm test',
// )

// -> Shared Library con closure simple (V5)
// nodePipelineV3(
//     appName: 'sonar-api-node',
//     nodeImage: 'node:22-alpine',
//     installCommand: 'npm ci',
//     testCommand: 'npm test',
//     lintCommand: 'npm run lint',
//     runLint: false
// )

// -> Shared Library con closure simple (V5)
// Con un closure simple

// nodePipelineV5 {
//     appName  = 'sonar-api-node'
//     nodeImage  = 'node:22-alpine'
//     installCommand  = 'npm ci'
//     testCommand  = 'npm test'
//     lintCommand  = 'npm run lint'
//     runLint  = false
// }


// -> Shared Library con closures anidados (V6)
// closures anidados DSL

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

// Ejemplo extra Pipeline DSL
// pipeline_template = "template_default"

// libraries {
// 	npm {
// 		image_tag = "20"
// 		directories_install = [".", "api"]
// 		directories_build = ["api"]
// 		isNewApiCatalog = true
// 		//clean_cache = true
// 	}
	
// 	serverless {
		
// 	}
	
// 	sonarqube {
// 		waitForQG = true
// 		stopQgError = true
// 	}
	
// 	email {
// 		to="email@gmail.com"
// 	}
// }

// application_environments {
// 	dev {
// 		credentials_id = "nombre_credencial"
// 		region = "us-east-1"
// 		stage = "DESA"
// 	}
	
// 	qa {
// 		credentials_id = "nombre_credencial"
// 		region = "us-east-1"
// 		stage = "TEST"
// 	}
	
// 	prod {
// 		credentials_id = "nombre_credencial"
// 		region = "us-east-1"
// 		stage = "PROD"
// 	}
// }