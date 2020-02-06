# Public Cloud Migration Case
This repo is a migration case study from on-premise to Amazon Web Services. You will see a set of solutions about the Business Requirements have been shaped by client.

The customer, you are working with, asks you to create a pilot for one of the company's applications in public
cloud. This is their first attempt to getting to know with non-on-prem environments so your responsibility is not only
delivering the solution but to present and highlight core concepts and benefits of the designed architecture. You can
choose any of major public cloud platforms; AWS, Azure or GCP, for the project.

# Current Architecture

<div class="sl-block is-focused" data-block-type="image" style="width: 806.4px; height: 265.853px; left: 73.6px; top: 399.073px; min-width: 1px; min-height: 1px;" data-origin-id="cdcaf465f82a9278da2789c9fe471672"><div class="sl-block-content" style="z-index: 12;"><img data-natural-width="1277" data-natural-height="421" style="" data-lazy-loaded="" src="https://s3.amazonaws.com/media-p.slid.es/uploads/913733/images/7027811/Screenshot_2020-02-04_at_14.35.00.png"></div></div>

Notejam is a web application which allows user to sign up/in/out and create/view/edit/delete notes. Notes can be
grouped in pads. Application is designed to be easy to build and run locally. Customer does recognize this may not be
the optimal architecture to meet the business requirements (see below). You are welcome to recommend (and
implement if time permits) changes to make it work better on target platform.

Notejam is currently build as monolith containing built-in webserver and SQLite database. Your task is to redesign the
architecture so it can meet business requirements on chosen public cloud platform.

Customer has provided you the GIT repo https://github.com/nordcloud/notejam with;

1. Documentation how to build and run application locally
2. Application source code
3. Unit tests for testing the application

NOTE: There are multiple implementations of Notejam application in different languages and frameworks. Pick the one
you are most comfortable with. All versions have identical functionality.

# Business Requirements

1. The Application must serve variable amount of traffic. Most users are active during business hours. During big events and conferences the traffic could be 4 times more than typical.
2. The Customer takes guarantee to preserve your notes up to 3 years and recover it if needed.
3. The Customer ensures continuity in service in case of datacenter failures.
The Service must be capable of being migrated to any regions supported by the cloud provider in case of emergency.
4. The Customer is planning to have more than 100 developers to work in this project who want to roll out multiple deployments a day without interruption / downtime.
5. The Customer wants to provision separated environments to support their development process for development, testing, production in the near future.
6. The Customer wants to see relevant metrics and logs from the infrastructure for quality assurance and security
purposes.

# Desired Outputs

The Customer wants you to present your solution and demonstrate the benefits supported by proper documentation.
provide all the source code produced in the creation of the infrastructure in an organized way.




