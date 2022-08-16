## test with snapshot version

```shell
base64 -d < secring.txt > secring.gpg
```

And then:

```shell
mvn -f rpm-builder-test -DkeyringFile=../secring.gpg -Drpm-builder.version=1.8.1-SNAPSHOT package rpm:rpm
```

Check:

```shell
rpmkeys --checksig --verbose '--define=_pkgverify_level signature' '--define=_pkgverify_flags 0x0' rpm-builder-test/target/*.rpm
```

Check with container:

```shell
podman run -v $(pwd)/rpm-builder-test/target:/usr/src:z --rm -ti registry.access.redhat.com/rhel7:latest bash -c 'rpmkeys --checksig --verbose "--define=_pkgverify_level signature" "--define=_pkgverify_flags 0x0" /usr/src/*.rpm'
podman run -v $(pwd)/rpm-builder-test/target:/usr/src:z --rm -ti registry.access.redhat.com/ubi8/ubi:latest bash -c 'rpmkeys --checksig --verbose "--define=_pkgverify_level signature" "--define=_pkgverify_flags 0x0" /usr/src/*.rpm'
podman run -v $(pwd)/rpm-builder-test/target:/usr/src:z --rm -ti registry.access.redhat.com/ubi9/ubi:latest bash -c 'rpmkeys --checksig --verbose "--define=_pkgverify_level signature" "--define=_pkgverify_flags 0x0" /usr/src/*.rpm'
```

## Acceptance

```shell
podman build -f containers/test-local .
```
