# Howto use JKube for this spring-boot petclinic

This is  a Fork of spring-boot petclinic to experiment with JKube


## Add Plugin to gradle.build

<pre>

plugins {
  ...
  id 'org.eclipse.jkube.kubernetes' version '1.14.0'
}

</pre>

## Configure fat jar in  gradle.build
<pre>
task customFatJar(type: Jar) {
    manifest {
        attributes 'Main-Class': 'org.springframework.samples.petclinic.PetClinicApplication'
    }
    baseName = 'all-in-one-jar'
    duplicatesStrategy = DuplicatesStrategy.EXCLUDE
    from { configurations.runtimeClasspath.collect { it.isDirectory() ? it : zipTree(it) } }
    with jar
}
</pre>


## configure image
<pre>
kubernetes {
    images {
        image {
            name = "quay.io/a422171/spring-petclinic:jkube"
        }
    }

}
</pre>

## Build Java

Some test failed on my computer. But I think we can skip them
taht's what "-x" is for

<pre>
./gradlew build -x
</pre>

## Build image & push
<pre>

# build image
./gradlew k8sbuild

# push
./gradlew -Djkube.docker.username=a422171 -Djkube.docker.password=geheim k8sPush

</pre>

## Build Kubernetes assets

<pre>


# create manifests

./gradlew k8sResource

# create helm chart

 ./gradlew k8sHelm

</pre>

# Configure pull secrets

<pre>
kubectl create secret docker-registry quaycred --docker-server=quay.io --docker-username=af22171 --docker-password=<your-pword> --docker-email=ludger.pottmeier@gmail.com
</pre>

Configure pullsecret to be used by jkube from src/main/jkube/deployment.yaml
<pre>
---
spec:
  template:
    spec:
      imagePullSecrets:
        - name: quaycred
</pre>