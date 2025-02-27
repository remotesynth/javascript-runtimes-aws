export AWS_ACCESS_KEY_ID ?= test
export AWS_SECRET_ACCESS_KEY ?= test
export AWS_DEFAULT_REGION = us-east-1

usage:       ## Show this help
	@fgrep -h "##" $(MAKEFILE_LIST) | fgrep -v fgrep | sed -e 's/\\$$//' | sed -e 's/##//'

install:     ## Install dependencies
	@which awslocal || pip install awscli-local

run:         ## Build, deploy, and invoke the Lambda container image locally
	docker build -t hello-world .; \
	repoUri=$$(awslocal ecr create-repository --repository-name hello-world --region us-east-1 | jq -r '.repository.repositoryUri'); \
	echo "$$repoUri"; \
	awslocal ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin $$repoUri; \
	docker tag hello-world:latest $$repoUri:latest; \
	docker push $$repoUri:latest; \
	AWS_PAGER="" awslocal lambda create-function --function-name hello-world --package-type Image \
		--code ImageUri=$$repoUri \
		--role arn:aws:iam::000000000000:role/lambda-role \
		--no-paginate; \
	 AWS_PAGER="" awslocal lambda create-function-url-config \
		--function-name hello-world \
		--auth-type NONE; \

.PHONY: usage install run
