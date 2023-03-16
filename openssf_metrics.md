# OpenSSF Metrics

> Pull Request Review (WIP): https://github.com/ietf-scitt/use-cases/pull/18

Collection of metric data into shared (crowdsourcable) DB. There are many repos
to search, we want to enable self reporting and granularity as applicable to
ad-hoc formed policy as desired by end-user.

2023-03-16: This will get reworked heavily as we align across https://codeberg.org/forgejo-contrib/discussions/issues/12, Rapunzel, and Alice

## activitypub extensions for security.txt

A methodology allowing organizations to nominate security contact points and policies via ActivityPub Actors.
This allows for notifications to be federated of new lifecycle events. These lifecycle events might be
VEX, SBOM, CSAF security advisory information, repository events, etc.

VEX documents should be aligned with the either the https://docs.oasis-open.org/csaf/csaf/v2.0/csaf-v2.0.html or OpenVEX specs: https://www.chainguard.dev/unchained/accelerate-vex-adoption-through-openvex. We can then communicate the IDs via ActivityPub like so.

## Summary  

When entities find security issues in source code, the correct channel to report security issues can be found if the repo has an RFC 9116 `security.txt` file with a `Contact` field. This contact field can be a URL which points to an ActivityPub Actor.

Via traversal of ActivityPub AcivityStream objects, reporters are enabled to discover reporting endpoints. Researchers are also enabled to receive up to date events by following declared ActivityPub Actors. When a researcher finds a vulnerability, they can submit their evidence to an [eNotary](https://scitt.io/components/enotary.html) (could be self notarized). The eNotary attests validity of the vuln and then replys to ActivityPub threads to facilite communication of valid vuln to upstream.

> Imagine the following YAML as a directed graph whose upleveled pesudocode form is:

```yaml
bob_vcs_repo:
  security.txt:
    Contact: https://activitypub.securitytxt.activitypub.example.org/bob

activitypub_service:
  endpoint_url: https://activitypub.securitytxt.activitypub.example.org
  actors:
    bob:
      attachment:
        type: "PropertyValue"
        name: "activitypubextensions"
        value: "<a href=\"https://activitypub.securitytxt.activitypub.example.org/users/bob/statuses/1\" target=\"_blank\" rel=\"nofollow noopener noreferrer me\"><span class=\"invisible\">https://</span><span class=\"\">activitypub.securitytxt.activitypub.example.org/users/bob/statuses/1</span><span class=\"invisible\"></span></a>"
      statuses:
        - id: "https://activitypub.securitytxt.activitypub.example.org/users/bob/statuses/1"
          content: "activitypubextensions"
          replies:
          - id: "https://activitypub.securitytxt.activitypub.example.org/users/bob/statuses/1/replies"
            type: "Collection"
            first:
              type: "CollectionPage"
              items:
              - "https://activitypub.securitytxt.activitypub.example.org/users/bob/statuses/2"
        - id: "https://activitypub.securitytxt.activitypub.example.org/users/bob/statuses/2"
          inReplyTo: "https://activitypub.securitytxt.activitypub.example.org/users/bob/statuses/1"
          content: "activitypubsecuritytxt"
          replies:
          - id: "https://activitypub.securitytxt.activitypub.example.org/users/bob/statuses/2/replies"
            type: "Collection"
            first:
              type: "CollectionPage"
              items:
              - "https://activitypub.securitytxt.activitypub.example.org/users/bob/statuses/3"
        - id: "https://activitypub.securitytxt.activitypub.example.org/users/bob/statuses/3"
          inReplyTo: "https://activitypub.securitytxt.activitypub.example.org/users/bob/statuses/2"
          content: "https://github.com/opencontainers/image-spec/raw/v1.0.1/schema/image-manifest-schema.json"
          replies:
          - id: "https://activitypub.securitytxt.activitypub.example.org/users/bob/statuses/3/replies"
            type: "Collection"
            first:
              type: "CollectionPage"
              items:
              - "https://activitypub.securitytxt.activitypub.example.org/users/bob/statuses/4"
        - id: "https://activitypub.securitytxt.activitypub.example.org/users/bob/statuses/4"
          inReplyTo: "https://activitypub.securitytxt.activitypub.example.org/users/bob/statuses/3"
          content: "bob.registry.example.org/src_repo_name_contents_are_webhook_translated_to_vcs_push_manifest:sha256@babebabe"
    alice:
      statuses:
        - id: "https://activitypub.securitytxt.activitypub.example.org/users/alice/statuses/1"
          inReplyTo: "https://activitypub.securitytxt.activitypub.example.org/users/bob/statuses/4"
          content: "alice.registry.example.org/vex_contents_are_openvex_from_scratch:sha256@babebabe"
```

- ActivityPub extensions for security.txt
  - Can you put things in `@context`?, yes. Unsure if other servers will propagate events.
  - It this piggybacking within the content approach interoperable today, yes.
- Somewhere, something happened
  - Bob tells Alice what happened
  - Alice decides, do I care about what whappened? (the federated event)
    - It's the triage process
     - https://github.com/intel/cve-bin-tool/issues/2639
     - Take upstream policy (attached to incoming via `inReplyTo` and or `replies`, you'd have to decide if you want to dereference these, perhaps based on reputaion of propagator to reduce attack impact)
- A container image was created (`FROM` rebuild chain)
  - Bob's forge tells Alice's forge, here's the content address uri for the manifest just pushed
  - Alice looks at the manifest, runs through all the packages she's maintaining in her forge
    - She applies the threat model of each as an overlay when determining if she wants to propagate into her internal environment
      - If any of these
  - Alice's downstream listener executes a system context to system context translation (grep: equilibrium, context-to-context)
    - She executs the shim
      - #1273
      - It parses the content in alignment with the schema
        - The shim already supports validation so we could actually just serialize the would be HTTP requests to files (same as staged when offline)
          - https://github.com/intel/dffml/pull/1273/files#r794027710
          - Could add activity style using this operation (function) as upstream, just copy paste and push to shim
            - https://github.com/intel/dffml/blob/e1914f794c7ccc3a7483fa490cfbe5170bf65972/dffml/util/testing/manifest/shim.py#L744-L757
    - https://github.com/tern-tools/tern#report-cyclonedxjson
      - Upload resulting SBOM to registry `FROM scratch` style or via
        - https://github.com/opencontainers/image-spec/blob/819aa940cae7c067a8bf89b1745d3255ddaaba1d/artifact.md
        - https://github.com/opencontainers/image-spec/blob/819aa940cae7c067a8bf89b1745d3255ddaaba1d/descriptor.md#examples
- A SBOM was published
  - Bob's forge uploads an SBOM to the registry
  - Alice's forge decides if she wants to propagate it (prioritizer, gatekeeper, umbrella)
    - Alice looks at the manifest, runs through all the packages she's maintaining in her forge
    - She applies the threat model of each as an overlay when determining if she wants to propagate into her internal environment
      - If any of these use similar components as were mentioned in this SBOM, propagate
  - Alice's listener receives the new SBOM event
    - She uploads a manifest instance of a SLURM submit job spec to her registry
      - https://slurm.schedmd.com/rest_api.html#slurmV0038SubmitJob
- A manifest instance of a IPVM/SLURM submit job was published to Alice's registry
  - Bob's forge uploads an SBOM to the registry
  - Alice's forge decides if she wants to propagate it (prioritizer, gatekeeper, umbrella)
    - Alice looks at the manifest, runs through all the packages she's maintaining in her forge
    - She applies the threat model of each as an overlay when determining if she wants to propagate into her internal environment
      - If any of these use similar components as were mentioned in this SBOM, propagate
  - Alice's listener within korifi receives the new SLURM submit job event
    - She downloads the job contents from the manifest
      - `FROM scratch`, `results.yaml` extraction style tar pipe
    - She executes the shim
    - The next phase parser runs kaniko

```mermaid
graph TD
    subgraph bob[Bob's Cool Software]
      actor[ActivityPub Actor - &#x0040 bob&#x0040 forge.mycoolsoftware.example.com]
      actor_attachment[Attachment PropertyValue activitypubsecuritytxt]
      activitypubsecuritytxt_root_post[activitypubsecuritytxt root post]
      activitypubsecuritytxt_vcs_push[vcs.push root post]
      activitypubsecuritytxt_vcs_push_content[vcs.push content - content address of manifest instance in registry]

      actor --> actor_attachment
      actor_attachment -->|Link| activitypubsecuritytxt_root_post
      activitypubsecuritytxt_vcs_push -->|inReplyTo| activitypubsecuritytxt_root_post
      activitypubsecuritytxt_vcs_push_content -->|inReplyTo| activitypubsecuritytxt_vcs_push
    end

    subgraph alice[Alice]
      alice_shouldi_contribute[Static Analysis Result] -->|inReplyTo| activitypubsecuritytxt_vcs_push_content
    end
```

  - Wait we're supposed to be doing KCP almost forgot
- Run some live ones in https://github.com/cloudfoundry/korifi via `dffml-service-http`
  - Demo similar job URL hash as registry tag based addressing of results within registry
  - Enable sending of AcivityPub events directly (later) or indirectly via proxy nodes (first, activitypub starter kit.
- https://ci.spdk.io/results/autotest-nightly/builds/1935/archive/crypto-autotest/build.log

```yaml
- completed_at: '2023-03-03T04:30:59Z'
  conclusion: success
  created_at: '2023-03-03T03:58:07Z'
  head_sha: 4241b49975cf364b540fc0ad961cde58e2c89623
  html_url: https://ci.spdk.io.deployed.at.example.com/public_build/autotest-spdk-master-vs-dpdk-main_1754.html
  id: 1754
  labels:
  - list
  - of
  - overlays
  - on
  - dffml.overlays.alice.shouldi.contribute
  name: alice.shouldi.contribute
  status: completed
  steps:
  - completed_at: '2023-03-03T04:26:42.000Z'
    conclusion: success
    name: Run scan
    number: 1
    started_at: '2023-03-03T04:26:40.000Z'
    status: completed
  url: https://vcs.activitypub.securitytxt.dffml.chadig.com/push/posts/40aeeda3-6042-42ed-8e32-99eff9bd8ef4
  workflow_name: Alice Should I Contribute?
```

- So no matter where you're executing, all the reporting and eventing is the same, because we are loosely coupled.
  - We can do `fromjson` in jq or we can do more advanced xargs chaining on the websocket for ad-hox dev work
  - We can shot from the activitypub inbox receiver to a message queue for integration with existing celery
  - This way we sidestep all rate limiting except for when we have to preform write events to GitHub
  - Otherwise we always read GitHub data from cypher queries over the reboardcast data
    - We can also have listeners which reboardcast the resolved contents of content address style broadcast data (the top level, so if this sees a container image uri broadcast, it would be pulling it down and maybe rebroadcasting the `results.yaml` or whatever is they transform needed to rebroadcast that data.
    - This is our onramp into the linked data space, eventually KERI for backing comms security
- https://linkeddatafragments.org/
- http://query.linkeddatafragments.org/#query=&resultsToTree=false&queryFormat=graphql
- https://gist.github.com/rubensworks/9d6eccce996317677d71944ed1087ea6
- https://github.com/comunica/jQuery-Widget.js/blob/master/config/config-default.json
- We need to turn the stream into something we can query using cypher or graphql-ld
- https://swordapp.github.io/swordv3/swordv3.html
- https://oras.land/blog/gatekeeper-policies-as-oci-image/
- https://github.com/project-zot/zot
- Okay if we can make the KERI SCITT instance use the OCI upload/download spec and then align the telemetry and registry federation protocols
  - Look into existing registry federation protocol if exists
- https://s3hh.wordpress.com/2022/10/27/oci-based-linux/
  - Similar goals to OS DecentrAlice
- https://github.com/project-machine/mos/releases/tag/0.0.7
- https://github.com/opencontainers/distribution-spec/blob/main/spec.md#endpoints
- https://github.com/opencontainers/distribution-spec/issues/388
  - Have we thought about federation protocols / APIs? To enable registries to propagate uploaded content within a network of registries? Looking to come up to speed on any existing discussion if that's been touched on. Thank you!
  - References
    - https://github.com/opencontainers/distribution-spec/blob/main/spec.md#endpoints
      - Looked here for relevant paths here but not seeing anything that looks like it's for notifications / inbox style eventing
    - https://github.com/sapcc/keppel
    - https://github.com/ietf-scitt/use-cases/issues/14
      - Hoping we can align to similar federation protocols across transparency services and container registries so event stream consumers can work with the same protocol for each (ActivityStreams/Pub?)
- https://conformance.opencontainers.org/
- https://vsoch.github.io/django-oci/docs/getting-started/auth
- https://vsoch.github.io/django-oci/docs/getting-started/testing
- https://github.com/opencontainers/distribution-spec/issues/110#issuecomment-708691114
- https://github.com/sapcc/keppel
- https://github.com/sapcc/keppel/blob/master/docs/api-spec.md#post-keppelv1authpeering
  - Looks like they have their own spec for federation, maybe we can implement with ActivityPub?
    - Maybe we can leverage the existing APIs similar to the /admin endpoint and just add in the activitypub endpoints for activitystreams / linked data notifications
- https://github.com/sapcc/keppel/blob/master/docs/example-policy.yaml
- We can take one manifest and make it into another one for execution via a different mechanism
  - Similar to the CLI overlays
    - https://github.com/intel/dffml/blob/c82f7ddd29a00d24217c50370907c281c4b5b54d/entities/alice/alice/please/contribute/recommended_community_standards/cli.py#L60-L72
  - This is also similar to how we can decouple TODO logging from content for `alice please log todos`
    - Operation to generate TODO body
    - Operation for logging the TODO (write to GitHub)
  - Similar to a mutation of the propagated event into something context local relevant
    - Yes this vuln affects due to instance policy relevant threat model overlays or not
- https://github.com/opencontainers/image-spec/blob/main/artifact.md
- Manifest for CLI command

**schema/alice/shouldi/contribute/github-com-omnilib-aiosqlite.json**

```json
{
    "@context": "https://github.com/intel/dffml/raw/alice/schema/schema/alice/shouldi/contribute/0.0.0.schema.json",
    "repo_url": "https://github.com/omnilib/aiosqlite"
}
```

- As container build

**schema/image/container/build/alice-shouldi-contribute-results-github-com-omnilib-aiosqlite.json**

```json
{
    "@context": "https://github.com/intel/dffml/raw/alice/schema/github/actions/build/images/containers/0.0.0.schema.json",
    "include": [
        {
            "branch": "alice",
            "build_args": "[[\"REPO_URL\", \"https://github.com/omnilib/aiosqlite\"]]",
            "commit": "ca92bfae5092bce908b70f6b5e0afbe242ce7a5b",
            "dockerfile": "entities/alice/scripts/alice-shouldi-contribute-results.Dockerfile",
            "image_name": "alice-shouldi-contribute-results-github-com-omnilib-aiosqlite",
            "owner": "intel",
            "repository": "dffml"
        }
    ]
}
```

- https://codeberg.org/fediverse/fep
- Open Source scanning flow
  - Upload manifest to registry
    - Federation event (send to follower /inbox)
      - content: `https://github.com/opencontainers/image-spec/raw/v1.0.1/schema/image-manifest-schema.json`
        inReplyTo: activitypub extensions for security.txt post URL for content `activitypubsecuritytxt`
      - content: container image uri uploaded
        inReplyTo: activitypub extensions for security.txt post URL for content `https://github.com/opencontainers/image-spec/raw/v1.0.1/schema/image-manifest-schema.json`
  - Downstream listener (aka delve into [config dict](https://intel.github.io/dffml/main/contributing/codebase.html?highlight=config+dict#config))
    - Federation event (send to follower /inbox)
      - content: `https://github.com/intel/dffml/raw/alice/schema/github/actions/build/images/containers/0.0.0.schema.json`
        inReplyTo: activitypub extensions for security.txt post URL for content `activitypubsecuritytxt`
      - content: `<extracted content(?)>`
        inReplyTo: activitypub extensions for security.txt post URL for content `https://github.com/intel/dffml/raw/alice/schema/github/actions/build/images/containers/0.0.0.schema.json`
  - Downstream listener
    - Republish watched `inReplyTo` schema into job/message queue
      - RabbitMQ
    - Message queue delivers to worker nodes
      - Kaniko job waiting for celery queue for image to build
        - Exit after rebuild and have orchestration manage respawn

```mermaid
graph LR

  subgraph vcs_source[Version Controled Software]
    subgraph dffml_vcs_source[dffml.git]
      subgraph dffml_vcs_source_security_txt[security.txt]
        dffml_vcs_source_security_txt_contact[Contact: https://example.org/dffml]
      end
      subgraph dffml_vcs_source_dockerfile[dffml.Dockerfile]
        dffml_vcs_source_dockerfile_from_base[FROM upstream as dffml]
      end
      subgraph dffml_vcs_source_dockerfile_example[dffml.example.Dockerfile]
        dffml_vcs_source_dockerfile_example_from_base[FROM dffml &#x0040 sha:latest]
      end
      subgraph vcs_source_alice[dffml.git/entities/alice]
        subgraph alice_vcs_source_security_txt[security.txt]
          alice_vcs_source_security_txt_contact[Contact: https://example.org/alice]
        end
        subgraph alice_vcs_source_dockerfile[alice.Dockerfile]
          alice_vcs_source_dockerfile_from_base[FROM dffml &#x0040 sha:latest]
        end
        subgraph alice_vcs_source_dockerfile_shouldi_contribute[alice_shouldi_contribute.Dockerfile]
          alice_vcs_source_dockerfile_shouldi_contribute_from_base[FROM alice &#x0040 sha:latest]
          subgraph alice_shouldi_contribute[alice shoulid contribute -keys ARG_REPO_URL]
            alice_shouldi_contribute_git_clone[git clone ...]
            alice_shouldi_contribute_read_security_txt[grep Contact: security.txt]
            alice_shouldi_contribute_result[Static Analysis Result]

            alice_shouldi_contribute_git_clone --> alice_shouldi_contribute_read_security_txt
            dffml_vcs_source_security_txt_contact --> alice_shouldi_contribute_read_security_txt
            alice_shouldi_contribute_read_security_txt --> alice_shouldi_contribute_result
          end
        end
      end
    end
  end
  
  subgraph schema[Manifest ADRs]
    subgraph manifest_build_images_contianers[Build Image Container]
      manifest_build_images_contianers_intent[README.md/THREATS.md]
      manifest_build_images_contianers_schema[1.0.0.schema.json]
    end
  end

  subgraph manifest_instances[Manifest Instances]
    alice_manifest_build_images_contianers_alice_shouldi_contribute
  end

  subgraph transparency_logs[Transparency Logs]
    dffml_scitt[dffml.scitt.example.org]
    alice_scitt[alice.scitt.example.org]
  end

  subgraph factory[Secure Software Factories]
    subgraph build_images_contianers[build_images_contianers.yml]
    end

    subgraph factory_container_image_registries[Container Image Registry https://oras.land]
      subgraph dffml_factory_container_image_registries_project[DFFML Images]
        dffml_container_image[dffml:latest]
      end
      subgraph alice_factory_container_image_registries_project[Alice Images]
        alice_container_image[alice:latest]
        alice_shouldi_contribute_scan_results[shouldicontribute &#x0040 sha384:babebabe]
      end
    end

    build_images_contianers --> dffml_scitt
    build_images_contianers --> alice_scitt
  end

  subgraph protocol_knowledge_graph_activity_pub[ActivityPub]
    subgraph ActivityPubExtensionsForSecurityTXT[activitypub extensions for security.txt]
      subgraph dffml_security_txt_contact[dffml.git/security.txt:Contact]
        dffml_actor[ActivityPub Actor - &#x0040 dffml &#x0040 example.org]
        dffml_actor_attachment[Attachment PropertyValue activitypubsecuritytxt]
        dffml_activitypubsecuritytxt_root_post[activitypubsecuritytxt root post]
        dffml_activitypubsecuritytxt_vcs_push[vcs.push root post]
        dffml_activitypubsecuritytxt_vcs_push_content[vcs.push content - content address of manifest instance in registry]

        dffml_actor --> dffml_dffml_actor_attachment
        dffml_actor_attachment -->|Link| dffml_activitypubsecuritytxt_root_post
        dffml_activitypubsecuritytxt_vcs_push -->|inReplyTo| dffml_activitypubsecuritytxt_root_post
        dffml_activitypubsecuritytxt_vcs_push_content -->|inReplyTo| dffml_activitypubsecuritytxt_vcs_push
      end

      subgraph alice_security_txt_contact[dffml.git/entites/alice/security.txt:Contact]
        alice_actor[ActivityPub Actor - &#x0040 alice &#x0040 example.org]
        alice_actor_attachment[Attachment PropertyValue activitypubsecuritytxt]
        alice_activitypubsecuritytxt_root_post[activitypubsecuritytxt root post]
        alice_activitypubsecuritytxt_vcs_push[vcs.push root post]
        alice_activitypubsecuritytxt_vcs_push_content[vcs.push content - content address of manifest instance in registry]

        alice_actor --> alice_actor_attachment
        alice_actor_attachment -->|Link| alice_activitypubsecuritytxt_root_post
        alice_activitypubsecuritytxt_vcs_push -->|inReplyTo| alice_activitypubsecuritytxt_root_post
        alice_activitypubsecuritytxt_vcs_push_content -->|inReplyTo| alice_activitypubsecuritytxt_vcs_push
      end
    end

    alice_actor -->|follow| dffml_actor
  end

  subgraph render_knowledge_graph_agora[Agora]
  end

  alice_vcs_source_dockerfile_shouldi_contribute

  dffml_vcs_source_security_txt_contact --> dffml_actor
  alice_vcs_source_security_txt_contact --> alice_actor

  alice_shouldi_contribute_result --> alice_shouldi_contribute_scan_results
  alice_shouldi_contribute_scan_results --> |inReplyTo| dffml_vcs_source_dockerfile_example_from_base

  dffml_container_image --> dffml_vcs_source_dockerfile_example_from_base
  alice_container_image --> alice_vcs_source_dockerfile_example_from_base

  dffml_vcs_source_dockerfile_example_from_base --> dffml_activitypubsecuritytxt_vcs_push
  dffml_activitypubsecuritytxt_vcs_push --> build_images_contianers_trigger
  alice_vcs_source_dockerfile_example_from_base --> alice_activitypubsecuritytxt_vcs_push

  alice_shouldi_contribute
```

```json
{
    "@context": [
        "https://www.w3.org/ns/activitystreams",
        "https://w3id.org/security/v1",
    ],
    "id": "https://mastodon.social/users/alice",
    "type": "Person",
    "following": "https://mastodon.social/users/alice/following",
    "followers": "https://mastodon.social/users/alice/followers",
    "inbox": "https://mastodon.social/users/alice/inbox",
    "outbox": "https://mastodon.social/users/alice/outbox",
    "featured": "https://mastodon.social/users/alice/collections/featured",
    "featuredTags": "https://mastodon.social/users/alice/collections/tags",
    "preferredUsername": "alice",
    "name": "Alice",
    "summary": "An ActivityPub Actor",
    "url": "https://mastodon.social/@alice",
    "publicKey": {
        "id": "https://mastodon.social/users/alice#main-key",
        "owner": "https://mastodon.social/users/alice",
        "publicKeyPem": "-----BEGIN PUBLIC KEY-----\nMIIBIjANBgk\n-----END PUBLIC KEY-----\n"
    },
    "attachment": [
        {
            "type": "PropertyValue",
            "name": "activitypubextensions",
            "value": "<a href=\"https://mastodon.social/users/alice/statuses/1\" target=\"_blank\" rel=\"nofollow noopener noreferrer me\"><span class=\"invisible\">https://</span><span class=\"\">mastodon.social/users/alice/statuses/1</span><span class=\"invisible\"></span></a>"
        }
    ],
    "endpoints": {
        "sharedInbox": "https://mastodon.social/inbox"
    }
}
```



```json
{
    "@context": [
        "https://www.w3.org/ns/activitystreams"
    ],
    "id": "https://mastodon.social/users/alice/statuses/1",
    "type": "Note",
    "summary": null,
    "inReplyTo": null,
    "published": "2022-11-11T04:40:17Z",
    "url": "https://mastodon.social/@alice/1",
    "attributedTo": "https://mastodon.social/users/alice",
    "to": [
        "https://www.w3.org/ns/activitystreams#Public"
    ],
    "cc": [
        "https://mastodon.social/users/alice/followers"
    ],
    "sensitive": false,
    "content": "activitypubextensions",
    "updated": "2022-11-11T04:42:27Z",
    "attachment": [],
    "replies": {
        "id": "https://mastodon.social/users/alice/statuses/1/replies",
        "type": "Collection",
        "first": {
            "type": "CollectionPage",
            "next": "https://mastodon.social/users/alice/statuses/1/replies?min_id=1&page=true",
            "partOf": "https://mastodon.social/users/alice/statuses/1/replies",
            "items": [
                "https://mastodon.social/users/alice/statuses/2"
            ]
        }
    }
}
```

- TODO for root nodes, should we be using a manifest instance?
  - https://github.com/intel/dffml/blob/alice/docs/arch/0010-Schema.rst
  - https://github.com/intel/dffml/blob/alice/docs/arch/0008-Manifest.md

```json
{
    "@context": [
        "https://www.w3.org/ns/activitystreams"
    ],
    "id": "https://mastodon.social/users/alice/statuses/2",
    "type": "Note",
    "summary": null,
    "inReplyTo": "https://mastodon.social/users/alice/statuses/1",
    "published": "2022-11-11T04:40:17Z",
    "url": "https://mastodon.social/@alice/2",
    "attributedTo": "https://mastodon.social/users/alice",
    "to": [
        "https://www.w3.org/ns/activitystreams#Public"
    ],
    "cc": [
        "https://mastodon.social/users/alice/followers"
    ],
    "sensitive": false,
    "content": "activitypubsecuritytxt",
    "updated": "2022-11-11T04:42:27Z",
    "attachment": [],
    "replies": {
        "id": "https://mastodon.social/users/alice/statuses/2/replies",
        "type": "Collection",
        "first": {
            "type": "CollectionPage",
            "next": "https://mastodon.social/users/alice/statuses/2/replies?min_id=2&page=true",
            "partOf": "https://mastodon.social/users/alice/statuses/2/replies",
            "items": [
                "https://mastodon.social/users/alice/statuses/3"
            ]
        }
    }
}
```

```json
{
    "@context": [
        "https://www.w3.org/ns/activitystreams"
    ],
    "id": "https://mastodon.social/users/alice/statuses/3",
    "type": "Note",
    "summary": null,
    "inReplyTo": "https://mastodon.social/users/alice/statuses/2",
    "published": "2022-11-11T04:40:17Z",
    "url": "https://mastodon.social/@alice/3",
    "attributedTo": "https://mastodon.social/users/alice",
    "to": [
        "https://www.w3.org/ns/activitystreams#Public"
    ],
    "cc": [
        "https://mastodon.social/users/alice/followers"
    ],
    "sensitive": false,
    "content": "https://github.com/opencontainers/image-spec/raw/v1.0.1/schema/image-manifest-schema.json",
    "updated": "2022-11-11T04:42:27Z",
    "attachment": [],
    "replies": {
        "id": "https://mastodon.social/users/alice/statuses/3/replies",
        "type": "Collection",
        "first": {
            "type": "CollectionPage",
            "next": "https://mastodon.social/users/alice/statuses/3/replies?min_id=3&page=true",
            "partOf": "https://mastodon.social/users/alice/statuses/3/replies",
            "items": [
                "https://mastodon.social/users/alice/statuses/4"
            ]
        }
    }
}
```

```json
{
    "@context": [
        "https://www.w3.org/ns/activitystreams"
    ],
    "id": "https://mastodon.social/users/alice/statuses/4",
    "type": "Note",
    "summary": null,
    "inReplyTo": "https://mastodon.social/users/alice/statuses/3",
    "published": "2022-11-11T04:54:56Z",
    "url": "https://mastodon.social/@alice/4",
    "attributedTo": "https://mastodon.social/users/alice",
    "to": [
        "https://www.w3.org/ns/activitystreams#Public"
    ],
    "cc": [
        "https://mastodon.social/users/alice/followers"
    ],
    "sensitive": false,
    "content": "registry.example.org/vex:sha256@babebabe",
    "attachment": [],
    "tag": [],
    "replies": {
        "id": "https://mastodon.social/users/alice/statuses/4/replies",
        "type": "Collection",
        "first": {
            "type": "CollectionPage",
            "next": "https://mastodon.social/users/alice/statuses/4/replies?only_other_accounts=true&page=true",
            "partOf": "https://mastodon.social/users/alice/statuses/4/replies",
            "items": []
        }
    }
}
```

- Now we want to translate to OpenVEX and have the content addresses of the signature for the post
  - https://github.com/package-url/purl-spec
  - https://github.com/openvex/spec/blob/main/OPENVEX-SPEC.md#example
- `statements.impact_statement` is Webhook payload object with SCITT enchancements
  - https://scitt.io/distributing-with-oci-scitt.html
  - Registry content addresses contains granular results (webhook payload, `alice shouldi contribute`, etc.)
    - Then our webhook watch on the registry publishes the replys
    - Or we update an example container with a pinned sha on the `FROM`
      - By watching the `push@vcs` (Version Control System) for the file
  - We should upload the VEX without the `@id` to the registry, then use that ID as the VEX
    `@id` when we `createPost()`.
    - Or better yet just have it do a kontain.me lightwieght proxy from the registry object

```json
{
  "@context": "https://openvex.dev/ns",
  "@id": "https://mastodon.social/users/alice/statuses/vex-sha256@feedface",
  "author": "GitHub Actions <actions@github.com>",
  "role": "GitHub Actions",
  "timestamp": "2023-02-02T14:24:00.000000000-07:00",
  "version": "1",
  "statements": [
    {
      "vulnerability": "vex-vcspush-sha256@feedface",
      "products": [
        "pkg:github/intel/dffml@ddb32a4e65b0d79c7561ce2bdde16d963c8abde1"
      ],
      "status": "not_affected",
      "justification": "vulnerable_code_not_in_execute_path"
      "impact_statement": "registry.example.org/vcspush:sha256@feedface",
    }
  ]
}
```

- **TODO** https://docs.oasis-open.org/csaf/csaf/v2.0/csaf-v2.0.html example
- We can now watch for events
  - **TODO** Update the following with the `xargs` call to https://github.com/genuinetools/reg (or equivilant) to do the double hop download of the VEX (without the `@id` right now because there is no proxy, just add on download), and then the subsquent manifest download of the vsspush webhook payload, then finally do the `.commits[].modified[]` selection
    - `$ websocat --exit-on-eof --basic-auth alice:$(cat ../password) ws://localhost:8000/listen/websocket | jq --unbuffered -r .object.id | xargs -l -I '{}' -- sh -c "curl -sfL '{}' | jq -r" &`
    - https://github.com/jakelazaroff/activitypub-starter-kit/pull/2

```console
$ curl -sfL https://vcs.example.org/push/outbox | jq --unbuffered -r '.orderedItems[].object.content' | grep stream_of | grep modified | jq -r --unbuffered '.commits[].modified[]'
Dockerfile
```

- Example use cases
  - DFFML 2nd party downstream rebuilds
    - https://github.com/intel/dffml/pull/1061/files#diff-c7d7828822f15922ed830bb6f3148edc97c291c809836b1a1808165d36bd8c9dR225-R229
      - https://github.com/pdxjohnny/dffml/blob/a7b2b0585862bda883be5f475a50945f91043b2f/docs/arch/0001-2nd-and-3rd-party-plugins.rst
        - ref: PR validation
    - Rebuild upstream container when we get an VEX (via AcivityPub) from upstream saying that any of the files we want to watch have changed
      - At first we will just watch all files within the downstream container build workflow
        - `on.workflow_dispatch && on.push.paths: ["https://github.com/intel/dffml.git#branch=main/*"]`
      - Later we will watch for the example container with the pinned version
        - `on.workflow_dispatch && on.push.paths: ["https://github.com/intel/dffml.git#branch=main/dffml/util/skel/common/Dockerfile"]`
        - `dffml/util/skel/common/Dockerfile`
          - `FROM registry.dffml.org/dffml:sha256@babebabe`

## Why?  

### Decentralized  

Actors can be spun up ad-hoc, mirrors decentralized nature of OSS development.

Enables projects to update based on policy.

```mermaid
sequenceDiagram
    SupportLevelOneSecondPartyPluginPullRequest->>+Webhook: 
    Webhook->>+ActivityPub: 
    ActivityPub->>+SupportLevelOneSecondPartyWatcher: 
    RequiredCIJobPassDownstreamSupportLevelOneSecondPartyPlugin->>+Webhook: 
    Webhook->>+ActivityPub: 
    SupportLevelOneSecondPartyPlugin->>+Webhook: 
    DFFML->>+Webhook: 
    Webhook->>+ActivityPub: 
    SupportLevelOneSecondPartyPluginPullRequest->>+Webhook: 
    Webhook->>+ActivityPub: 
    ActivityPub->>+DFFMLTutorialPullRequestBumpPin: 
    ExampleDockerFileWithPinnedVersion->>+ContainerBuild: 
    ContainerBuild->>+ExampleDockerFileWithPinnedVersion: 
    ExampleDockerFileWithPinnedVersion->>+Webhook: 
    ActivityPub->>+DFFMLTutorialPullRequestBumpPin: 
    DFFMLTutorialPullRequestBumpPin->>+Webhook: 
```

> Upstream of following mermaid: https://github.com/intel/dffml/tree/alice/docs/tutorials/rolling_alice/0000_architecting_alice#what-is-alice

```mermaid
graph BT
    subgraph Alice[Alice the Entity]
        subgraph compute[Compute]
        Web5[Web 5]
        KCP
        CI_CD[CI/CD]
        end
        subgraph soul[Strategic Plans and Principles]
        Threat_Modeling[Threat Modeling]
        Debug
        end
        subgraph collector[Collector]
        subgraph dynamic_analysis[Dynamic Analysis]
            policy[policy.yml]
            sandbox_policy_generator[Adaptive Sandboxing]
        end
        subgraph static_analysis[Static Analysis]
            cve_bin_tool[CVE Binary Tool]
            SBOM
        end
        end
        Open_Architecture
        Open_Architecture[Alice the Open Architecture]
        snapshot_system_context[Alice the Overlay<br>Snapshot of System Context]
        orchestartor[Orchestartor]


        Open_Architecture --> Threat_Modeling
        Open_Architecture --> Debug

        Threat_Modeling --> orchestartor
        Debug --> orchestartor

        orchestartor --> KCP
        orchestartor --> Web5
        orchestartor --> CI_CD

        CI_CD --> snapshot_system_context
        KCP --> snapshot_system_context
        Web5 --> snapshot_system_context

        snapshot_system_context --> sandbox_policy_generator
        snapshot_system_context --> cve_bin_tool

        sandbox_policy_generator --> policy --> Open_Architecture
        cve_bin_tool --> SBOM --> Open_Architecture
        cve_bin_tool --> VEX -->|Trigger validation run of mitigation suggestion| orchestartor
        policy -->|Check if policy says out of scope<br>client vs. server usage| VEX
    end
```

- https://github.com/opencontainers/image-spec/blob/main/manifest.md
  - Image command sequence to in-toto
  - Attestation as build arg
  - Still eventually [#1426](https://github.com/intel/dffml/issues/1426)
- https://docs.github.com/en/actions/using-workflows/triggering-a-workflow#accessing-and-using-event-properties
  - Example of bots managing pinning
- Mirror of CI/CD can be executed with same manifest instance pattern for increased performance

```console
$ curl -fL https://vcs.activitypub.securitytxt.dffml.chadig.com/push/outbox/ > outbox@push@vcs.activitypub.securitytxt.dffml.chadig.com
$ jq .orderedItems[].id < outbox\@push\@vcs.activitypub.securitytxt.dffml.chadig.com | wc -l
3931
$ jq -r '.orderedItems[] | [{(.id): (.object.content)}] | .[] | add' < outbox\@push\@vcs.activitypub.securitytxt.dffml.chadig.com | jq -R --unbuffered '. as $line | try (fromjson | .) catch $line'
$ jq -r '.orderedItems[] | [{(.id): (.object.content)}] | .[] | add' < outbox\@push\@vcs.activitypub.securitytxt.dffml.chadig.com | jq -R --unbuffered '. as $line | try (fromjson | .workflow_job) catch $line'
$ jq -r '.orderedItems[] | [{(.id): (.object.content)}] | .[] | add' < outbox\@push\@vcs.activitypub.securitytxt.dffml.chadig.com | jq -c -R --unbuffered '. as $line | try (fromjson | .workflow_job) catch $line' | jq -s | python3 -c "import sys, pathlib, json, yaml; print(yaml.dump(json.load(sys.stdin)))"
```

---

- Downstream

```console
$ curl -ku alice:$(cat ../password) -X POST -v http://localhost:8000/admin/follow/push/vcs.activitypub.securitytxt.dffml.chadig.com/443/https
* Uses proxy env variable no_proxy == 'localhost,127.0.0.0/8,::1'
*   Trying 127.0.0.1:8000...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
* Server auth using Basic with user 'alice'
> POST /admin/follow/push/vcs.activitypub.securitytxt.dffml.chadig.com/443/https HTTP/1.1
> Host: localhost:8000
> Authorization: Basic YWxpY2U6ODkyZTI1Y2MwMTMzYTcwYTEzMzRlYTIyNmQ2NDNkNTNhMDRjYzc5MDIwOWM0MzY1ZTUwMzA2Mjc3MGVmZTdmOWVlM2M3MDI4OWNlODdiYzJmZThiYzE2NGNlNTQxYTYx
> User-Agent: curl/7.68.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 204 No Content
< X-Powered-By: Express
< ETag: W/"a-bAsFyilMr4Ra1hIU5PyoyFRunpI"
< Date: Mon, 13 Feb 2023 14:50:51 GMT
< Connection: keep-alive
< Keep-Alive: timeout=5
<
* Connection #0 to host localhost left intact
```

- Upstream

```
Dumbo listening on port 8000…
GET /push 200 1493 - 7.432 ms
Data to compare (request-target): post /push/inbox
host: vcs.activitypub.securitytxt.dffml.chadig.com
date: Mon, 13 Feb 2023 14:50:49 GMT
digest: SHA-256=4byRebHbzxk6BlJopQYVQcI+9YiHojWKhaI2S0J8w68=
Data to sign (request-target): post /alice/inbox
host: d30a15e2d986dc.lhr.life
date: Mon, 13 Feb 2023 14:50:50 GMT
digest: SHA-256=QOPUiXd5oq6u0i+DNQu9TZRIydnRewGdlN1eoiaEsKs=
GET /push 200 1493 - 1.654 ms
POST /push/inbox 204 - - 1557.550 ms
```

- Rebase and cleanup
  - `HEAD` is 6 commits, at 9d16b1fe04b5e880be59d6fcddde698cfd036b2f
- Redeploy upstream

```console
$ curl -sfL https://github.com/pdxjohnny/activitypub-starter-kit/archive/refs/heads/alternate_port.tar.gz | tar xvz
$ cd activitypub-starter-kit-alternate_port
$ cat > .env <<'EOF'
# The Node environment
NODE_ENV="production"

# The path to the database schema
SCHEMA_PATH="db/schema.sql"

# The path to the database file
DATABASE_PATH="db/database.sqlite3"

# The hostname (i.e. the "example.com" part of https://example.com/alice)
HOSTNAME="vcs.activitypub.securitytxt.dffml.chadig.com"

# The account name (i.e. the "alice" part of https://example.com/alice)
ACCOUNT="push"
EOF
$ npm i
$ head -n 10000 /dev/urandom | sha384sum | awk '{print $1}' | tee ../webhook
$ head -n 10000 /dev/urandom | sha384sum | awk '{print $1}' | tee ../password
$ openssl genrsa -out keypair.pem 4096 && openssl rsa -in keypair.pem -pubout -out publickey.crt && openssl pkcs8 -topk8 -inform PEM -outform PEM -nocrypt -in keypair.pem -out pkcs8.key
$ mkdir node_modules/@types/simple-proxy-agent/
$ echo "declare module 'simple-proxy-agent';" | tee node_modules/@types/simple-proxy-agent/index.d.ts
$ npm run build
$ FDQN=vcs.activitypub.securitytxt.dffml.chadig.com WEBHOOK_PATH=$(cat ../webhook) NODE_ENV=production PORT=8000 ACCOUNT=push ADMIN_USERNAME=admin ADMIN_PASSWORD=$(cat ../password) PUBLIC_KEY=$(cat publickey.crt) PRIVATE_KEY=$(cat pkcs8.key) npm run start

> dumbo@1.0.0 start
> node build/index.js

Dumbo listening on port 8000…
GET /push 200 1493 - 8.201 ms
GET /push 200 1493 - 1.200 ms
POST /push/inbox 204 - - 1583.186 ms
```

- Redeploy downstream and send follow request

```console
$ rm -f db/database.sqlite3; ssh -R 80:localhost:8000 nokey@localhost.run 2>&1 | tee >(grep --line-buffered 'tunneled with tls termination' | awk -W interactive '{print $1}' | xargs -l -I '{}' -- sh -c 'reset; echo "{}"; PROTO=https FDQN="{}" WEBHOOK_PATH=$(cat ../webhook) NODE_ENV=production PORT=8000 ACCOUNT=alice ADMIN_USERNAME=alice ADMIN_PASSWORD=$(cat ../password) PUBLIC_KEY=$(cat publickey.crt) PRIVATE_KEY=$(cat pkcs8.key) npm run start &
c4d2dfa777b86f.lhr.life

> dumbo@1.0.0 start
> node build/index.js

Dumbo listening on port 8000…
GET /alice 200 1354 - 2.530 ms
GET /alice 200 1354 - 0.895 ms
POST /alice/inbox 204 - - 71.294 ms
POST /admin/follow/push/vcs.activitypub.securitytxt.dffml.chadig.com/443/https 204 - - 3183.157 ms
$ curl -ku alice:$(cat ../password) -X POST -v http://localhost:8000/admin/follow/push/vcs.activitypub.securitytxt.dffml.chadig.com/443/https
$ websocat --exit-on-eof --basic-auth alice:$(cat ../password) ws://localhost:8000/listen/websocket
```

- Create post on upstream

```console
$ cat > post.json <<'EOF'
{
    "object": {
        "type": "Note",
        "content": "OUR PROPHECY MUST BE FULFILLED!!! https://github.com/intel/dffml/pull/1401#issuecomment-1168023959"
    }
}
EOF
$ curl -u admin:$(cat ../password) -X POST --header "Content-Type: application/json" --data @post.json -v http://localhost:8000/admin/create
POST /admin/create 204 - - 133.004 ms
```

- Restarted the ssh tunnel and followed again
  - Response seen from downstream websocket listener

```json
{
    "@context": "https://www.w3.org/ns/activitystreams",
    "type": "Create",
    "published": "2023-02-13T15:39:08.628Z",
    "actor": "https://vcs.activitypub.securitytxt.dffml.chadig.com/push",
    "to": [
        "https://www.w3.org/ns/activitystreams#Public"
    ],
    "cc": [
        "https://eb62a3437cf6a9.lhr.life/alice"
    ],
    "object": {
        "attributedTo": "https://vcs.activitypub.securitytxt.dffml.chadig.com/push",
        "published": "2023-02-13T15:39:08.628Z",
        "to": [
            "https://www.w3.org/ns/activitystreams#Public"
        ],
        "cc": [
            "https://vcs.activitypub.securitytxt.dffml.chadig.com/push/followers"
        ],
        "type": "Note",
        "content": "OUR PROPHECY MUST BE FULFILLED!!! https://github.com/intel/dffml/pull/1401#issuecomment-1168023959",
        "id": "https://vcs.activitypub.securitytxt.dffml.chadig.com/push/posts/15f4de9c-a582-4f9d-8372-a740a5ffe6a8"
    },
    "id": "https://vcs.activitypub.securitytxt.dffml.chadig.com/push/posts/58f883cd-0252-4319-a934-3ca2eb062f62"
}
```

![hack-the-planet-hackers-gif](https://user-images.githubusercontent.com/5950433/191852910-73787361-b00c-4618-bc5e-f32d656bbf0f.gif)

---


- Just FYI, have been playing with the idea of using security.txt contact as an AcivityPub Actor to advertise things such as delegate Actors for various purposes. For example, list via attachments actors which publish content addresses of an orgs SBOMs This would enable leveraging ActivityPub as a means for definition and broadcast for entities delegated to various roles. We could do the same for the 3rd parties to advertise what actors are within which roles, aka are authorized to say this thing is FIPs certified. We could then attach SCITT receipts to these: https://github.com/intel/dffml/discussions/1406?sort=new#discussioncomment-4794771
  - The SCITT registry then becomes the quick lookup path (analogously database view) to verify this. This way end users don't have to traverse the full Knowledge Graph (Activity Pub in this case). Receipt we care about for verification would be is this `inReplyTo` DAG hop path valid, aka is `did:merkle` in SCITT.
  - Can have a thread linked in attachments for manifests, can discover from there
    - Can watch for replies and execute jobs based off listening for manifest instances `inReplyTo` to the manifest.
      - Post content addresses of manifest existing in oras.land (a container "image" registry)
        - `FROM scratch`
          - [Alice Engineering Comms: 2023-01-19 @pdxjohnny Engineering Logs](https://github.com/intel/dffml/discussions/1406?sort=new#discussioncomment-4729296)
  - https://github.com/WebOfTrustInfo/rwot11-the-hague/blob/master/advance-readings/Enhancing_DID_Privacy_through_shared_Credentials.md
  - https://github.com/WebOfTrustInfo/rwot11-the-hague/blob/master/draft-documents/did-merkle.md
- Looks like we can have four attachments, we can make one link to a post as an attachment, then replies to that to build more trees of data
- https://policymaker.disclose.io/policymaker/introduction

---

- References
  - RFC9116: https://securitytxt.org/
  - https://github.com/ietf-scitt/use-cases/issues/14
  - https://github.com/openvex/spec/issues/9
  - https://forum.forgefriends.org/t/about-the-friendly-forge-format-f3/681
    - > ForgeFed is an ActivityPub extension. ActivityPub is an actor-model based protocol for federation of web services and applications.
  - https://mastodon.social/@ariadne@treehouse.systems/109784681116604896
    - > meanwhile at work, a thing i've been working on for the past few months has dropped: https://www.chainguard.dev/unchained/accelerate-vex-adoption-through-openvex it's basically like ActivityStreams, but for security vulnerability data sharing. with a little bit of work, we can lift up to something more like ActivityPub for real-time collaboration, a blog is forthcoming about it.
  - aka the Manifest Transport ADR
    - Associated Alice tutorial: [Rolling Alice: Architecting Alice: Stream of Consiousness](https://github.com/intel/dffml/blob/alice/docs/tutorials/rolling_alice/0000_architecting_alice/0005_stream_of_consciousness.md)
      - https://social.treehouse.systems/@ariadne/109808644259234008
        - We'll want to align with Ariadne's Rapunzel
          - [Alice Engineering Comms: 2023-02-06 Engineering Logs](https://github.com/intel/dffml/discussions/1406?sort=new#discussioncomment-4883572)
- TODO
  - [ ] OIDC to keypair to post replys (fulcio?)
    - Or just the noterizing proxy

- [service: sw: src: change: notify: Service to facilitate poly repo pull model dev tooling: activitypubsecuritytxt](https://github.com/intel/dffml/issues/1315#issuecomment-1416392795)
  - Reproduced below
- We seek interop with Aradine's Rapunzel
- The Agora: a Knowledge Commons
  - https://docs.google.com/document/d/1DXJRDh9Ss5VCRBi3oirDw9d7yjn3H2hMqfN2ETTyjIc/edit#
  - We seek interop with the Agora

Scratch work upstream: https://github.com/intel/dffml/discussions/1406?sort=new#discussioncomment-4819872

- The Open Architecture (Alice) sits at the interesction of CI/CD, Security, and AI/ML.
  - We metion Alice here as a follow on who's development sees this use case as critical
- Think cross between review system (SCITT as the proof, TDB on identity preknown at this point, OpenSSF members stream 8 vuln sharing CCF ledger)
- https://scitt.io/distributing-with-oci-scitt.html
- https://www.darkreading.com/dr-tech/cybersecurity-nutrition-labels-still-a-work-in-progress
  - https://www.whitehouse.gov/briefing-room/statements-releases/2022/10/20/statement-by-nsc-spokesperson-adrienne-watson-on-the-biden-harris-administrations-effort-to-secure-household-internet-enabled-devices/
    - > Yesterday, the White House convened leaders from the private sector, academic institutions, and the U.S. Government to advance a national cybersecurity labeling program for Internet-of-Things (IoT) devices. The Biden-Harris Administration has made it a priority to strengthen our nation’s cybersecurity, and a key part of that effort is ensuring the devices that have become a commonplace in the average American household – like baby monitors or smart home appliances – are protected from cyber threats. A labeling program to secure such devices would provide American consumers with the peace of mind that the technology being brought into their homes is safe, and incentivize manufacturers to meet higher cybersecurity standards and retailers to market secure devices.
      >
      > Yesterday’s dialogue focused on how to best implement a national cybersecurity labeling program, drive improved security standards for Internet-enabled devices, and generate a globally recognized label. Government and industry leaders discussed the importance of a trusted program to increase security across consumer devices that connect to the Internet by equipping devices with easily recognized labels to help consumers make more informed cybersecurity choices (e.g., an “EnergyStar” for cyber). These conversations build on the foundational work that has been pioneered by the private sector and the National Institute of Standards and Technology (NIST) to help build more secure Internet-connected devices. It also follows President Biden’s Executive Order on Improving the Nation’s Cybersecurity, which highlighted the need for improved IoT security and tasked NIST, in partnership with the Federal Trade Commission, to advance improved cybersecurity standards and standardized product labels for these devices.
- https://csrc.nist.gov/publications/detail/white-paper/2022/11/09/implementing-a-risk-based-approach-to-devsecops/final
  - > DevOps brings together software development and operations to shorten development cycles, allow organizations to be agile, and maintain the pace of innovation while taking advantage of cloud-native technology and practices. Industry and government have fully embraced and are rapidly implementing these practices to develop and deploy software in operational environments, often without a full understanding and consideration of security. Also, most software today relies on one or more third-party components, yet organizations often have little or no visibility into and understanding of how these components are developed, integrated, deployed, and maintained, as well as the practices used to ensure the components’ security. To help improve the security of DevOps practices, the NCCoE is planning a DevSecOps project that will focus initially on developing and documenting an applied risk-based approach and recommendations for secure DevOps and software supply chain practices consistent with the Secure Software Development Framework (SSDF), Cybersecurity Supply Chain Risk Management (C-SCRM), and other NIST, government, and industry guidance. This project will apply these DevSecOps practices in proof-of-concept use case scenarios that will each be specific to a technology, programming language, and industry sector. Both closed source (proprietary) and open source technology will be used to demonstrate the use cases. This project will result in a freely available NIST Cybersecurity Practice Guide.
- https://www.intel.com/content/www/us/en/newsroom/news/2022-intel-innovation-day-2-livestream-replay.html#gs.djq36o
  - Similar to the software labeling, with Alice we are trying to cross these streams
    - Datasheets for Datasets
      - https://arxiv.org/abs/1803.09010
      - > The machine learning community currently has no standardized process for documenting datasets, which can lead to severe consequences in high-stakes domains. To address this gap, we propose datasheets for datasets. In the electronics industry, every component, no matter how simple or complex, is accompanied with a datasheet that describes its operating characteristics, test results, recommended uses, and other information. By analogy, we propose that every dataset be accompanied with a datasheet that documents its motivation, composition, collection process, recommended uses, and so on. Datasheets for datasets will facilitate better communication between dataset creators and dataset consumers, and encourage the machine learning community to prioritize transparency and accountability.

> Side from Andrew Ng's Intel Innovation 2022 Luminary Keynote
> Source: https://www.intel.com/content/www/us/en/newsroom/news/2022-intel-innovation-day-2-livestream-replay.html#gs.iex8mr
> ![image](https://user-images.githubusercontent.com/5950433/193330714-4bcceea4-4402-468f-82a9-51882939452c.png)

- Possible alignment with Andrew's "Data-Centric AI"
 - is the discipline of systematically engineering the data used to build an AI system
   - This is what we're doing with Alice
- Possible alignment with Andrew's "The iterative process of ML development"
  - https://github.com/intel/dffml/tree/alice/docs/tutorials/rolling_alice/0000_architecting_alice#entity-analysis-trinity
  - Intent / Train model
    - Establish correlations between threat model intent and collected data / errors (telemetry or static analysis, policy, failures)
  - Dynamic analysis / Improve data
    - We tweak the code to make it do different things to see different data. The application of overlays. Think over time.
  - Static / Error analysis
    - There might be async debug initiated here but this maps pretty nicely conceptually since we'd think of this as a static process, we already have some errors to analyze if we're at this step.

*Gif of Alice on roller skates throwing a bowling ball which is a software vuln, strike, she frontflips throwing knife style throws the pins into pull requests. We zoom out and see her just doing this over and over again around the Entity Analysis Trinity. Intent/LTM is where the throwing board is. Bowling alley is static analysis and the end of the bowling ally where she frontflips over (through hoop of CI/CD fire?) is where she pics up the pins and throws them as pull request (titles and numbers maybe, pulls/1401 style maybe?) knives into the board at the top which is the LTM and codebase. Then from top, LTM to static analysis where bowling alley starts shes in the lab, cooking up the vuln or maybe out looking for it. Or maybe refactoring after pull requests!*

![Entity Analysis Trinity](https://user-images.githubusercontent.com/5950433/188203911-3586e1af-a1f6-434a-8a9a-a1795d7a7ca3.svg)

## Misc. Notes

Mike from OpenSSF has been thinking about SCITT as a schema
and rules on how one would assert facts, weither it's
confidential compute or traditional permissions is impelmenetation details.

- TODO
  - Going with Authenticated Chained Data Containers and KERI Watchers as an implmentation of SCITT for this
    - It's basically a decentralized pubsub event notification methodology that can be done over ACDC piggybacking on ActivityPub as layer 7.
    - Event data lives "off chain" in a container registry secured via existing transparency service based methods (KERI, SCITT, SigStore), where the chain is the network of keys involved for a train of thoughts comms between entities. Since we transmit ActivityPub over KERI, the graph of our supply chain data we are sharing can be shared with trusted actors who agree not to be duplicitous, and who's KERI keys can be tied back to TEEs so that we can confirm they are running software that doesn't intend (via ML-based, Alice, analysis) to be duplicitous. We can now have our trusted computing based for decentralized compute, aka CI/CD pipelines delivering across project trust boundries.
  - Go over `.github/workflows/alice_shouldi_contribute.yml` which is called as reusable
    workflow using SLSA demos as conceptual upstream.
    - This gives us metric collection with overlays applied to input network which log
      data provenance later used via policy referenced in a Living Threat Model
      of a downstream end user facing application. The use case document will cover
      how we effectivly self notarize the data provenance assisted by the OIDC token
      issused to the workflow from GitHub.
  - Align with Software Supply Chain Artifact Examples use case
    - https://github.com/ietf-scitt/use-cases/pull/17/files
  - As CI/CD velocity increases, we approach a similar threat model to distributed
    compute, address here.
    - https://github.com/intel/dffml/blob/alice/docs/tutorials/rolling_alice/0000_forward.md#supply-chain-security
  - As consumer, how can I discover that fact and trust that it's accruate
  - Could imagine a world where things like Scorecard express the data as as SCITT attestation
  - You go and query that (source code data mining data or vuln data) store (via DID resolution)
    and you say tell me everythig you know about foo and you get it all back.
    - Addresses OpenSSF/Christine's: Looking at trying to connect all the different data sources
  - Until we have an implementation with Web5 (https://github.com/TBD54566975) that's at at least beta, we could expore what that looks like.
  - Open Architecture: need to track usages / `reuse` of contexts `ictx`, `nctx`, etc. with
    something predeclared, aka at runtime if your `Operation` data structure doesn't
    allowlist your usage of it you can pass it to a subflow for reuse.
    This allows us to use the format within our orchrestration and for static analysis
    because we can use this same format to describe the trust boundry proeprties that
    other domain sepcific represenatations of architecture have, for instance we could
    if we were doing and Open Architecture (OA) Intermediate Representation (IR) for
    and ELF file we might note that the input network context is not reused from
    the top level system context. Where as if we did an OA IR for Python code we
    would say that the input network is reused from the top level system context
    (it has access to that memory region, whereas when you launch and ELF you look
    access to the parents memory region, typically).
- References
  - https://scitt.io/distributing-with-oci-registries.html
    - https://github.com/ucan-wg/invocation/pull/1/files#diff-b335630551682c19a781afebcf4d07bf978fb1f8ac04c6bf87428ed5106870f5R85
  - https://github.com/intel/dffml/blob/alice/docs/arch/0009-Open-Architecture.rst
  - https://github.com/intel/dffml/blob/alice/docs/arch/0008-Manifest.md
  - https://www.cisa.gov/sites/default/files/publications/VEX_Use_Cases_Aprill2022.pdf
  - [2022-09-29 IETF SCITT Technical Meeting](https://github.com/intel/dffml/discussions/1406#discussioncomment-3763647)
  - https://github.com/transmute-industries/did-eqt/blob/main/docs/did-eqt-opa-primer.md#securing-did-method-operations-with-opa
  - https://datatracker.ietf.org/doc/html/draft-birkholz-scitt-architecture
  - https://www.w3.org/2022/07/pressrelease-did-rec.html.en
  - https://mastodon.social/@pdxjohnny/109804930974811967
  - https://docs.microsoft.com/en-us/azure/confidential-ledger/architecture
    - In search of more easy options to faciliate public/private hybrid chains
      of supply chain data. ideally OSS (SSI Service?).
  - Similar work to address
    - https://docs.google.com/presentation/d/1WF4dsJiwR6URWPgn1aiHAE3iLVl-oGP4SJRWFpcOlao/edit#slide=id.g14078b5bab0_0_517
- We first issue the verifiable credentials, with a reference to the rest of the hashlinks (or whatever the CBOR style thing is)
  - Open Source Decentralized Identifiers and Verifiable Credentials Infrastructure and Tooling - verifiable-data/Hashlink.ts at c80cab9abe4db478add16b14837ba9a3afc3a70f
  - Then we hashlink that, and that's the top level ref. effectivly the evolution of the zephyr.stripped shasum patchset
  - https://github.com/zephyrproject-rtos/zephyr/pull/51954
- Future
  - As a follow on to the OpenSSF Metrics use case document and
    [Living Threat Models are better than Dead Threat Models](https://www.youtube.com/watch?v=TMlC_iAK3Rg&list=PLtzAOVTpO2jYt71umwc-ze6OmwwCIMnLw)
    [Rolling Alice: Volume 1: Coach Alice: Chapter 1: Down the Dependency Rabbit-Hole Again](https://github.com/intel/dffml/blob/alice/docs/tutorials/rolling_alice/0001_coach_alice/0001_down_the_dependency_rabbit_hole_again.md)
    will cover how we identify and query provenance on dependencies where caching
    on data flow execution is assisted via quering public SCITT infrastructure
    and sourcing cached state from trustworthy parties.
  - Leveraging our restoration of cached state from trustworthy parties and
    LTM policy we can measure alignment of ML model's used within BOM components
    so as to report conceptual alignment with entity strategic plans / principles.
    - We care about data provenance. This provenance could be for example on
      inference derived from provenance from training data and model training
      env and config. This will allow us to ensure the prioritizer make
      decisions based on Sprit of the law / aka intent based policy derived from
      [Trinity of Static Analysis, Dynamic Analysis, and Intent](https://github.com/intel/dffml/tree/alice/docs/tutorials/rolling_alice/0000_architecting_alice#entity-analysis-trinity).
    - Living Threat Model threats, mitigations, trust boundaries as initial data
      set for cross domain conceptual mapping of the the trinity to build pyramid
      of thought alignment to strategic principles.
    - One of our strategic plans / principles might say
      - "We must be able to trust the sources of all input data used for all
        model training was done from research studies with these ethical
        certifications"
    - This allows us to write policies (Open Policy Agent to JSON to DID/VC/SCITT
      translation/application exploration still in progress) for the organizations
      we form and apply them as overlays to flows we execute where context appropriate.
      These overlaid flows define the trusted parties within that context as applicable
      to the active organizational policies as applicable to the top level system context.
    - The policy associated with the principle that consumes the overlaid trust
      attestations we will implement and LTM auditor for which checks
      the SCITT provenance information associated with the operation implementations and
      the operation implementation network, input network, etc. within the orchestrator
      trust boundary.
  - https://time.crystals.prophecy.chadig.com
    - https://twitter.com/OR13b/status/1621907110572310528
      - Actor `acquire`
        - `attachments` `Link` to `activitypubextensions` thread
        - `content: "activitypubextensions"` thread
          - `inReplyTo: "$activitypubextensions_thread", content: "https://time.crystals.prophecy.chadig.com/bulk.1.0.0.schema.json"` thread
            - This becomes analogous to shared stream of consciousness uniform API for submitting across contexts (Manifests).
              - CI/CD across projects with different orchestrators for downstream validation of the 2nd and 3rd party plugin ecosystem.
                - This facilitates communication across pipelines across repos across PRs so we can use versioned learning to promote across trust boundaries (3rd party to 2nd party or support level 2 to 1)
                - #1207
                - #1315
                - Alice helps us see risk over time, this is where we see Coach Alice, cartography used applied to dev branches, we grow closer to distributed compute with this, as iteration time is on dev branches rather than release or main
                  - This will probably be part of Alice and the Health of the Ecosystem
      - Ask him to reply to `@acquire@time.crystals.prophecy.chadig.com`
    - ActivityPub Actor watches for messages replying to certain threads
    - Actor creates pull request to https://github.com/OR13/endor style repo
      - Actor creates didme.me and gets VC SCITT receipt for associated `did:pwk:` (committed into Endor fork, he'd used git as database)
        - This could also be our content address of something in oras.land
        - In the AI training data/human case we see the input data (meme) validated via SCITT
          - We want to enable application of policy to data set ingestion, because this will happen in MLOps aka CI/CD
           - Workstream: AI Ethics
        - In the CI/CD use case, we see the input data (manifest referenced content, images, packages, metrics data output `FROM scratch` OpenSSF metrics use case) validated via SCITT.
        - Later we build up the threat modeling for the dynamic analysis portion of Alice which plays with input data as changes to repos and connects more of our Data, Analysis, Control for the software development process.
      - Actor replies to Orie's reply with his receipt for his time crystals.
    - For k8s style or OS DecentAlice style deployments (OSS scanning feeding OpenSSF metrics) we could run the graphed trust / event chain to a sidecar ActivityPub Actor / root of trust.

---

- RCFv1 Feedback
  - https://mailarchive.ietf.org/arch/msg/scitt/dowMkmWhbi9Pkq6B5DhdXzip0so/
  - > Maik: I'm curious to understand this more in detail. I think so far we've been thinking about the term federation as accepting SCITT claims/receipts from one transparency service in another transparency service, based on some policy. I think what you're describing is more about subscriptions/broadcasting/... Can you describe a little more on how you see this working in a SCITT transparency service? It might be that this is something that sits outside of it, but I'm not fully sure yet, so I wanted to understand it better first.
  - Sketch response notes
    - Policy as code, but sometimes it needs to execute because it's context aware, it's trying to decide for each piece of data that's pubsub'd for federation, does this SCITT instance want to be serving that trust assertion
    - Open Policy Agent
    - JSONLD
    - Cypher
    - Verfiable Credentials
      - https://github.com/transmute-industries/jsonld-to-cypher
    - > SCITT claims/receipts from one transparency service in another transparency service, based on some policy
      - Policy as compute contract, exec result determines entry of broadcast/subscribed syned SCITT instances (OSS projects, 2nd party, walled gardens)
        - Can use this methodlolgy to broadcast those policies to topics via inReplyTo or later maybe service endpoint (as relay compute contact via did:oa)
        - TDX self attest/DICE style layering where we wrap the receipt with the proposed insersion policy
          - https://github.com/TrustedComputingGroup/DICE
          - Something about the decentralized compute and the compute contract sandboxing to enable distributed trust propagation
            - The AI travel in sandboxes. This is how trust (Alice) surfs the hypergraph. 🏄‍♀️ It's like ePBF but IPVM but as k8s admission controller but on scitt /inbox
              - What's the sandbox? The policy here, what's the policy? The compute contract defining what the schema?+OPA?+cypher and what are the content addresses of the upstream contracts needed to fulfill the query
              - On of the goals with the SCITT federation via ActivityPub is that it's a step towards the event stream being all JSONLD. Then audit and policy are effectively all done with definitions within DID referenced Verifiable Credentials. These encapsulate a receipt for a claim which who's insertion policy is a (or a context address of) policy as code aka compute contract. That contract statically defines or fulfils fetching or generating whatever data is needed to validate for insertion or federation and executes within a sandboxed environment. These policies can be overlayed with instance local additional policy as code. We can then read this event stream from anywhere or graft new trust chains off of it. GAUC is awesome it's just centralized from what I can tell, which is perfect for a performant view into a decentralized ecosystem. I think the two will work great together. We're all thinking in the same directions from what I can tell, just different goals in terms of data sovereignty, GUAC-GPT on the centralized side, Alice on the decentralized side.. The reason for the heavy focus on decentralization is that it for CI/CD we need to be able to spin dev and test chains of trust ad-hoc, for the AI side, we need to spin them for offline use cases tied to the users root of trust, or viewed as the user + their hardware roots of trust. Decentralized primitives allow us to never be forced to trust any authority other than what the deployment use case needs, scoping privilege to the threat model.
                - This means that we can always do "one hop" analysis from this format set (DID+VC aka LDVC2)
                  - [util: testing: manifest: shim: Initial commit intel/dffml#1273](https://github.com/intel/dffml/pull/1273)
                  - Alice status update
          - Something about it being recursive
            - If your log inserted it and you want to federate that insertion to my log I want to know why you thought this was valid to insert. Tell me the scientific evidence Alice. What's the analysis report, why should that entry also get admission into my log. It's dynamic evaluation, so I can say based on my overlays okay I will take that receipt in my log based on that scientific method/process but due to this isntamces special overlays I won't take this other one. Maybe this is a recursion into the concept of a notary but required "self" notary required for that instances overlays applied to incoming receipt scientific processes / ipvm / data flow / open architecture.
          - Decentralized trust propagation
            - why should this be in your internal CA set sort of thing, what's your roots of trust and how to we justify adds / removes / modifications and notify that we think it's in your best interest for you to also trust the propagation of this trust assertion
          - Decentralized dependency review - hence Open SSF Metrics use case
            - Open Source maintiners general attidute: If you're going to tell my open source project what I should or should not do (use as a dependency) you better tell me why, or else the opne source repo / org SCITT we are propigating this to will deney based on it's execution of policy as code with it's overlays applied
            - https://github.com/intel/dffml/blob/alice/docs/tutorials/rolling_alice/0001_coach_alice/0007_cartographer_extraordinaire.md
              - ref: [I am not a supplier, Thomas Depierre](https://www.softwaremaxims.com/blog/not-a-supplier)
    - https://mailarchive.ietf.org/arch/msg/scitt/5SDINK63mr1BWX-BzbcsvalVLOA/
      - > As part of this service offering example.com only allows artifacts to be
        > added to the ledger by government customers who paid for the service.
        > Example.com also requires a certain level of identity proofing and
        > multi-factor authentication because it offers sensitive services to
        > their government customers. Auditors require an even high level of
        > identity proofing and authentication.
        >
        > This policy of example.com is dictated by their business model and is
        > outside the scope of what the IETF SCITT will define.
    - https://mailarchive.ietf.org/arch/msg/scitt/cgz-9oif4SLMbdLyPn0P6-E8cIY/
      - > This is interesting - many thanks Hannes. I notice our spec includes Merkle trees as the database structure - seems like an implementation detail, i.e. just a database. Can an implementer use, for example, an otherwise secured and RBAC'd record structure such as a file system or relational/hierarchical/sharded db, or is distributed ledger mandatory?
      - [df: overlay: Implement middleware/RBAC chains of ordered applications of overlays #1400](https://github.com/intel/dffml/issues/1400)
    - There exist N instances of SCITT, which ones are tied to the current executing system context? Those determine reality, aka what is real, what to trust, for that system context
      - Hallucinating large language models train/chain of thought is tied to context local SCITT. We graft in (accept new federated claims/receipts) from other system contexts we trust. We trust based on if we should propagate from that context to this context. Equilibrium as reality. Context local reality for the net. If they are isolated, they define their own reality.
      - The truth will set you free. Equilibrium of record propagation.
      - Everything is true, everything is permitted (insert policy*)
        - Most of the time what's permitted is context dependent, overlays
        - Time is context local, Earth is *current* most likely context, we have to ensure we factor in changes to this via abstraction of "when", ref lunar comms, beyond
          - Vol 6: Where are your NTP gods now?
        - Physics, also context local, gravity differs on different parts of Earth, and elsewhere. Make no assumptions, always policy as code, always recursive. Always show my why I should believe this message, message as the entity, not the messanger (instance federating from), focus on the truth in the message when propagating, not who propagates, critical thinking, threat modeling, deployment contexts
- Fork and exec over ActivityPub over DWN CLI
  - https://github.com/soda480/wait-for-message-action
    - **TODO** Fork and add ActivityPub support
    - Usage of this in a `needs` -> `matrix/workflow_run/workflow_dispatch/ipvm` is below
  - Closing the loop. Fork and exec via Distributed Compute, oras.land, VCS events ActivityPub over DWN (CLI) with DID VC SCITT with DID:KERI backed attested compute rooted identities. Enabling decentralized hardware roots of trust to facilitate decentralized asynchronous supply chains. Open source software development with end to end encrypted grafted chains communication only via attested channels for comms on vuln discovery with responsible disclosure and communication of remediation.
    - This is also how we communicate
      - https://intel.github.io/dffml/main/examples/webhook/
      - https://www.youtube.com/watch?v=vqfSvU80W8Q&t=777s
    - Reference entity actions for this use case
      - https://intel.github.io/dffml/main/examples/webhook/webhook.html#webhook-dataflow
        - next tutorial: kcp -> k8s -> cf push -> webhook service -> dataflow to create activitypub event -> dwn-cli send -> webrtc -> dwn-cli recv -> `alice threats listen activitypub -stdin` -> `alice shouldi contribute` -> `alice please contribute` -> soft-serve/github repo pull request -> webhook service
      - https://www.youtube.com/watch?v=TMlC_iAK3Rg&list=PLtzAOVTpO2jYt71umwc-ze6OmwwCIMnLw&t=2064s
      - https://www.youtube.com/watch?v=THKMfJpPt8I&list=PLtzAOVTpO2jYt71umwc-ze6OmwwCIMnLw&t=128s
      - https://github.com/charmbracelet/soft-serve
      - https://codeberg.org/forgejo/forgejo/issues/363
        - Where is the best place to discuss federation of CI? Maybe in the spec repo? Shall I just throw up a pull request on that GitLab with the schema? We're interested in folks rebroadcasting their GitHub webhooks, etc. into the ActivityPub space so as to enable live at HEAD in poly repo envs (to help secure rolling releases).

---

Post RFCv3

- It's all the signal
  - New data event
    - inReplyTo context manifest, there was a new vex, there was a new registry image, there was a new something which will be interpreted in a context aware way based of reply set as label as input to running system context watching
    - What is in the new data event?
      - Where can I get the content (registry)
      - How do I access it?
        - https://identity.foundation/presentation-exchange/#presentation-definition
- for gatekeeper policy options
  - https://www.seedwing.io/
