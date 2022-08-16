FROM docker.io/library/rockylinux:8 AS builder

RUN mkdir /tmp/import-keys

COPY private.key /tmp/import-keys
COPY public.key /tmp/import-keys

RUN true \
    && cd /tmp/import-keys \
    && gpg --batch --passphrase rpmbuildertest --import private.key \
    && gpg --import public.key \
    && rpm --import public.key

RUN gpg --list-secret-keys --keyid-format LONG

RUN true \
    && mkdir -p $HOME/.gnupg \
    && cd $HOME/.gnupg \
    && gpg --batch --passphrase rpmbuildertest --pinentry-mode loopback --export-secret-keys > secring.gpg \
    && gpg --show-keys secring.gpg

COPY rpm-builder-test/target/rpm-builder-test-1.0-SNAPSHOT.noarch.rpm /test.rpm

RUN rpm -qvi /test.rpm

# now create the YUM repository

RUN yum -y install createrepo_c findutils

RUN true \
    && mkdir /myrepo \
    && cp /test.rpm /myrepo \
    && cd /myrepo \
    && createrepo .

RUN find /myrepo

# test with RHEL 7
FROM registry.access.redhat.com/rhel7:latest

COPY test.repo /etc/yum.repos.d/test.repo
RUN ls /etc/yum.repos.d/

COPY --from=builder /myrepo/ /myrepo/

RUN yum repo-pkgs test list || true

COPY public.key /tmp/import-keys/
RUN ls /tmp/import-keys/
RUN rpm --import /tmp/import-keys/public.key
RUN yum -y install rpm-builder-test


# test with RHEL 8
FROM registry.access.redhat.com/ubi8/ubi:latest

COPY test.repo /etc/yum.repos.d/test.repo
RUN ls /etc/yum.repos.d/

COPY --from=builder /myrepo/ /myrepo/

RUN yum repo-pkgs test list || true

COPY public.key /tmp/import-keys/
RUN ls /tmp/import-keys/
RUN rpm --import /tmp/import-keys/public.key
RUN yum -y install rpm-builder-test


# test with RHEL 9
FROM registry.access.redhat.com/ubi9/ubi:latest

COPY test.repo /etc/yum.repos.d/test.repo
RUN ls /etc/yum.repos.d/

COPY --from=builder /myrepo/ /myrepo/

RUN yum repo-pkgs test list || true

COPY public.key /tmp/import-keys/
RUN ls /tmp/import-keys/
RUN rpm --import /tmp/import-keys/public.key
RUN yum -y install rpm-builder-test
