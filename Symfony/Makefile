user := $(shell id -u)
group := $(shell id -g)
dc := USER_ID=$(user) GROUP_ID=$(group) docker-compose
dr := $(dc) run --rm
exec := docker exec projectname_php_1 # Must be modified for each project
sy := $(exec) php bin/console

.DEFAULT_GOAL := help
.PHONY: help install docker-build clean prod test tw dev migration migrate seed analyse rollback
help: ## Affiche les commandes
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

install: vendor/autoload.php package-lock.json ## Installe les dépendances

build-docker: ## Build l'image php
	$(dc) build

clean: ## Kill les volumes
	docker-compose -f docker-compose.yml down --volumes

dev: clean vendor/autoload.php ## Lance l'environnement de développement
	$(dc) up

prod: package-lock.json ## Lance le build du dossier assets
	npm run build

migration: vendor/autoload.php ## Génère une migration
	$(sy) make:migration

migrate: vendor/autoload.php ## Migre la base de données
	$(sy) doctrine:migrations:migrate -q

rollback: vendor/autoload.php
	$(sy) doctrine:migration:migrate prev -q

seed: vendor/autoload.php ## Charge les fixtures dans la base de données
	$(sy) doctrine:fixtures:load -q
	$(sy) doctrine:schema:validate -q
	$(sy) doctrine:fixtures:load -q

test: vendor/autoload.php ## Lance les tests PHPUnit
	php bin/phpunit

tw: vendor/autoload.php ## Lance le watcher pour les tests PHPUnit
	./vendor/bin/phpunit-watcher watch

analyse: vendor/autoload.php
	./vendor/bin/phpstan analyse

vendor/autoload.php: composer.lock
	$(dr) composer install
	touch vendor/autoload.php

package-lock.json: package.json
	npm install