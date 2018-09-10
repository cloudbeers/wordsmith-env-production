def label = "$env.JOB_BASE_NAME-${UUID.randomUUID().toString()}"
podTemplate(label: label, yaml: """
apiVersion: v1
kind: Pod
spec:
    containers:
    - name: jnlp
    - name: kubectl
      image: lachlanevenson/k8s-kubectl:v1.10.7
      command:
      - cat
      tty: true
    - name: curl
      image: appropriate/curl
      command:
      - cat
      tty: true
"""
) {
    node(label) {
        checkout scm
        container('kubectl') {
            def environment = readYaml file:"environment.yaml"
            def activeEnvironment = environment["active"]
            def namespace = environment["namespace"]

            sh """
                sed -e 's/NAMESPACE/${namespace}/g' \
                -e 's/ACTIVEENVIRONMENT/${activeEnvironment}/g' \
                production.yaml | kubectl apply -f - 
            """

            sh "kubectl get svc,ingress -n ${namespace} -o yaml"
        }
    }

}
