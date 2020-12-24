#!/usr/bin/env groovy

def label = "ops-portal-${UUID.randomUUID().toString()}"

podTemplate(name: label, label: label, containers: [
  containerTemplate(
    name: 'ruby', 
    image: 'ruby:2.6.5',
    ttyEnabled: true, 
    command: 'cat',
    envVars: [
      envVar(key: 'LANG', value: 'C.UTF-8')
    ]
  )
]) {
  node(label) {
    stage ('Checkout repo') {
      checkout scm
    }
    container('ruby') {
      ansiColor('xterm') {
        stage('Install Ruby') {
          sh 'ruby --version'
          sh 'gem install bundler -v 1.17.3'
          sh 'bundle install --jobs=3 --retry=3'
        }
        stage('Rubocop') {
          sh 'bundle exec rubocop --config .rubocop.yml'
        }
        stage('Linters') {
          sh 'bundle exec jsonlint app/conf/*.json'
          sh 'bundle exec rake lint:task-types'
          sh 'bundle exec rake lint:action-types'
        }
        stage('Rspec') {
          sh 'bundle exec rspec --colour --tty'
        }
      }
    }
  }
}
