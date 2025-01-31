---
layout     : post
author     : Alan Tai
date       : 2024-01-12
title      : How not to host your React app on a supercomputer
subtitle   : Downsizing your cloud service like a savvy Brit
image      : posts/2024/01/12/kraken.jpeg
categories : Kubernetes
---
Right, kettle on, crumpets in the toaster, a client with a static React app as basic as a cuppa. Just HTML, CSS, and enough JavaScript to animate a loading spinner. Yet, somehow, they are [convinced](https://hemanthmgowda.medium.com/how-to-serve-a-static-html-page-using-nginx-in-a-kubernetes-cluster-e4d2123879aa) to deploy this digital daisy to the world of Kubernetes cluster with enough [power](https://www.reddit.com/r/spacex/comments/2i07nk/what_is_the_total_power_of_an_f9_at_liftoff/) to launch a SpaceX rocket… yep, just another day in the life of a budget-conscious Brit dev.

![Falcon Heavy launching (Image from SpaceX via X)](/assets/img/posts/2024/01/12/spacex.960x0.jpg)

Ah, a sprightly little React app, a digital billboard proclaiming your existence to the web, crafted with lovingly hand-coded JavaScript and maybe a sprinkling of CSS like some fancy fairy dust. But for some, hosting this digital dandelion is akin to swatting a fly with a bazooka. We’re talking behemoth virtual machines with enough memory to store the [Queen’s corgi collection](terraform state mv keycloak_authentication_bindings.this keycloak_authentication_bindings.bindings_it) in high-definition, and bandwidth rivalling the [River Thames at high tide](https://www.youtube.com/watch?app=desktop&v=4jpYGyqe1Jo). It’s like using a nuclear reactor to boil an egg or summoning a lumbering cargo plane to deliver a single teabag — unnecessary and downright expensive, enough to make [the King himself raise an eyebrow](https://www.reuters.com/world/king-charles-raises-eyebrows-with-his-tie-choice-amid-uk-greece-dispute-2023-12-01/) at the cloud bill.

Imaging it, picture it if you will: your minimalist masterpiece, a mere morsel of code, nestled amongst Kubernetes pods designed to power Amazon on Black Friday. Every byte served is like powering [the Large Hadron Collider](https://home.cern/science/accelerators/large-hadron-collider), every page loads a symphony of wasted resources. Meanwhile, your credit card whimpers like a kicked puppy with each monthly bill. Surely, there must be a better way (and it doesn’t involve remortgaging your house)! Fear not, dear over-engineered friends, for this techie tea break isn’t just about pointing out the absurdity. We’re going to brew up some sensible solutions, tailored to fit your React app like a hand-knitted jumper from your nan. So put down the fancy cloud brochure and grab a custard cream, because we’re about to explore the world of lightweight, budget-friendly hosting options that won’t leave you with a bill fit for [Buckingham Palace](https://www.rct.uk/visit/buckingham-palace). Your bank account and the planet will thank you.

## Hosting a client-side rendering React app with Kubernetes: A journey into overkill

> _npm run build_

That’s probably how you generate your humble static website. A few HTML files, and maybe a dash of CSS — what could be simpler? Hosting it, however, is where things get… interesting, especially for us developers. Why settle for boring old shared hosting when we can wield the mighty hammer of Kubernetes, accompanied by the trusty sidecar Nginx Ingress, right? Buckle up, friends, for we’re about to embark on a journey of glorious, unnecessary complexity. It’s gonna be a bumpy, acronym-filled ride!

### Step 1: Charting the uncharted

First, we need a Helm Chart. Not just any Helm Chart, mind you, but one so intricately crafted it could win a “Most YAML YAMLs” award. We’ll have [cascading manifests](https://helm.sh/docs/chart_template_guide/subcharts_and_globals/), [conditional rendering](https://helm.sh/docs/chart_template_guide/control_structures/) if statements so nested they’d make an Escher drawing blush, and enough values.yaml parameters to fill a small library. Remember, complexity is our middle name (the first is probably “Over”).

### Step 2: Ingress-ing the inevitable

Our precious website needs a gateway, a bouncer who ensures only the purest HTTP requests enter. Enter Nginx Ingress, resplendent in its virtual server blocks and location directives. Why use a simple port mapping when you can have a virtual server farm with custom annotations and rewrite rules that would make Kafka himself dizzy? Bonus points if you configure health checks that ping your website every millisecond, just to be sure it’s still… existing.

### Step 3: Taming the Docker beast

Next, let’s write some containerised magic. Dockerfiles? Pah! We’ll craft a [multi-stage](https://docs.docker.com/build/guide/multi-stage/) monstrosity with Alpine Linux, Nginx, and a custom-built binary that simply echoes “Hello, world!”. Remember, the simpler the solution, the less we’ve learned, right? Security? What security? Who needs pesky things like sandboxes and permissions when you have the power of root? Grant your container full access to everything — the host system, your neighbour’s cat, the meaning of life.

### Step 4: Summoning the Kraken

We need a Kubernetes cluster. Obviously. A single Raspberry Pi won’t do — we need a cloud of virtual machines so vast it makes Bezos wince. Bootstrapping, configuring, managing — it’s like wrangling a herd of [angry yaks](https://news.cgtn.com/news/354d544e31597a6333566d54/share_p.html), except the yaks are made of bits and bytes. But hey, the feeling of accomplishment when that “Running” status finally appears? Priceless.

### Step 5: Helm-ing the storm

Now, the moment of truth: releasing the Helm Chart into the Kubernetes wild. A mere formality. Watch as our YAML manifests dance across the screen, weaving a tapestry of pods, deployments, and services that would make even the most battle-hardened sysadmin weep. But wait, what’s this? Our website deployment failed? Don’t worry, that’s just Kubernetes whispering sweet nothings in the form of cryptic error logs. Spend hours poring over them, deciphering cryptic acronyms like “EPFD” and “Liveness Probe Failure.”

### Step 6: Scaling to success

Finally, we scale our website to a million replicas, just in case someone’s grandma wants to see our cat pictures at peak traffic. Watch in awe as your cluster groans under the weight of containerised felines, while your CPU usage graph resembles a toddler’s finger painting gone nuclear. But hey, at least you can brag about having the most horizontally scaled cat website on the Internet!

### Step 7: Monitoring the mayhem

With our website humming, it’s time to monitor its every twitch. Prometheus and Grafana dashboards [sprout like mushrooms after a rainstorm](https://www.angi.com/articles/common-yard-mushrooms.htm), each one displaying metrics to monitor every byte, pod, and packet with the intensity of a hawk watching a field of mice. Every blip on the graph is a potential disaster, every dip in traffic a harbinger of the apocalypse. Remember, a flatlined graph means you’ve achieved true enlightenment (or your website is dead, one of the two).

### Step 8: Victory!

Congratulations! You’ve successfully hosted a static website using a technology stack designed to orchestrate fleets of containerised applications for Fortune 500 companies. Did it take ten times longer and cost a small fortune? Absolutely! Remember, in the land of over-engineering, the journey is the destination, and the more hair-pulling moments, the merrier!

So, the next time you’re tempted to host your website with just some basic hosting, remember: where’s the fun in that? Kubernetes and Nginx Ingress await, ready to transform a simple task into a gloriously over-engineered odyssey. Just don’t tell your manager about the cloud bill.

## From Kubernetes hindrance to storage bucket Zen

Let’s be honest, folks. Spinning up a Kubernetes cluster for a static site is like wearing a full suit of armour to a game of hopscotch. Overkill? You bet your bottom byte it is! Fear not, weary devs, for there’s a better way — a path to hosting Nirvana where simplicity reigns and your sanity remains unskewed. Why wrestle with YAML tentacles when you have the elegant simplicity of a good old-fashioned storage bucket?

Imagine this: you upload your static files to a digital filing cabinet in the cloud called a “storage bucket” in GCP (or S3 in AWS), spacious enough to hold all your HTML, CSS and JS treasures. No need for Docker Hub logins or cryptic Kubernetes manifests. Deployment? Just drag and drop, sip your tea, and voila! The files are nestled snugly in the cloud, ready to be served. Scaling? Automatic, like magic beans for website traffic. Resource utilisation? A purring kitten compared to the Kubernetes kraken.

Now, the star of the show — the load balancer. Think of it as a posh waiter, directing web visitors to your files like a seasoned London cabbie. No more pod puzzles, no more scaling sorrows — the load balancer handles it all, smoother than a cuppa after a long day.

The setup? Easier than ordering pizza (and with fewer toppings!). One click, two clicks, a sprinkle of configuration magic, and voila! Your website is basking in the warm glow of the Internet, ready to serve up its delights to the masses.

And to prove it’s not just hippy dippy mumbo jumbo, let’s whip up some Terraform code that’ll have your static website singing like a rockstar.

```bash
terraform {
  required_version = ">= 1.6.5"

  backend "gcs" {
    bucket = "rockstar_project_terraform_states"
    prefix = "terraform/state"
  }

  required_providers {
    google = {
      source  = "hashicorp/google"
      version = ">= 5.10"
    }

    tls = {
      source  = "hashicorp/tls"
      version = ">= 4.0"
    }

    acme = {
      source  = "vancluever/acme"
      version = ">= 2.19"
    }

    local = {
      source  = "hashicorp/local"
      version = ">= 2.4"
    }
  }
}

provider "google" {
  region = var.region
  zone   = var.zone
}

provider "acme" {
  server_url = "https://acme-v02.api.letsencrypt.org/directory"
}

locals {
  website_buckets = [
    for pair in setproduct(var.environments, var.static_website_bucket_names) : {
      environment = pair[0]
      name        = "rockstar_project_${pair[0]}_${pair[1]}"
    }
  ]
}

resource "google_storage_bucket" "rockstar_project_static_websites" {
  for_each = {
    for bucket in local.website_buckets : bucket.name => bucket
  }

  project                     = data.google_project.this.project_id
  name                        = each.value.name
  location                    = var.region_storage
  uniform_bucket_level_access = false
  force_destroy               = true

  # Set the default file to retrieve when none is specified, i.e. URLs ending with a slash
  # Not-found page redirection is needed if this is a React SPA
  website {
    main_page_suffix = "index.html"
    not_found_page   = strcontains(each.value.name, "react") ? "index.html" : "404.html"
  }

  # Depends on your needs, setting CORS headers could make it easier for development
  cors {
    origin = [
      "*",
    ]

    method = [
      "GET",
      "HEAD",
      "PUT",
      "POST",
      "DELETE",
      "PATCH",
    ]

    response_header = [
      "*",
    ]

    max_age_seconds = 300
  }
}

# Make the files in the bucket publicly accessible
resource "google_storage_default_object_acl" "rockstart_project_static_websites" {
  for_each = {
    for bucket in local.website_buckets : bucket.name => bucket
  }

  bucket = each.value.name

  role_entity = [
    "READER:allUsers",
  ]
}

# Define a backend for the load balancer in dev environment
resource "google_compute_backend_bucket" "rockstart_project_static_website_dev" {
  name        = "static_website_dev"
  bucket_name = google_storage_bucket.rockstar_project_static_websites["rockstar_project_static_website_dev"].name
  enable_cdn  = true

  cdn_policy {
    default_ttl = 300
    client_ttl  = 300
    max_ttl     = 3600
  }

  # This would make local development easier but not recommended in production
  custom_response_headers = [
    "Access-Control-Allow-Origin: *",
  ]
}

# Define a backend for the load balancer in staging environment
resource "google_compute_backend_bucket" "rockstart_project_static_website_staging" {
  name        = "static_website_staging"
  bucket_name = google_storage_bucket.rockstar_project_static_websites["rockstar_project_static_website_staging"].name
  enable_cdn  = true

  cdn_policy {
    default_ttl = 300
    client_ttl  = 300
    max_ttl     = 3600
  }

  # This would make local development easier but not recommended in production
  custom_response_headers = [
    "Access-Control-Allow-Origin: *",
  ]
}

# Get a public IP address for the load balancer
resource "google_compute_global_address" "loadbalancer" {
  name = "loadbalancer"
}

# Tell the load balancer how you'd like the traffic to be redirected to different storage bucket backends
resource "google_compute_url_map" "websites" {
  name            = "websites"
  default_service = google_compute_backend_bucket.rockstart_project_static_website_dev.self_link

  host_rule {
    hosts = [
      "dev.${var.domain}",
    ]

    path_matcher = "rockstart-project-static-website-dev"
  }

  host_rule {
    hosts = [
      "staging.${var.domain}",
    ]

    path_matcher = "rockstart-project-static-website-staging"
  }

  path_matcher {
    name            = "rockstart-project-static-website-dev"
    default_service = google_compute_backend_bucket.rockstart_project_static_website_dev.self_link
  }

  path_matcher {
    name            = "rockstart-project-static-website-staging"
    default_service = google_compute_backend_bucket.rockstart_project_static_website_staging.self_link
  }
}

# Generate a private key of a SSL certificate for signing your HTTPS domain name, not needed for HTTP-only websites
resource "tls_private_key" "website" {
  algorithm = "RSA"
}

# Tell our ACME provider to use our private key for certificate generation
resource "acme_registration" "website" {
  account_key_pem = tls_private_key.website.private_key_pem
  email_address   = var.acme_email
}

resource "google_compute_ssl_certificate" "rockstart_project_static_website_dev" {
  name        = "rockstart-project-static-website-dev"
  private_key = acme_certificate.rockstart_project_static_website_dev.private_key_pem
  certificate = acme_certificate.rockstart_project_static_website_dev.certificate_pem
}

resource "google_compute_ssl_certificate" "rockstart_project_static_website_staging" {
  name        = "rockstart-project-static-website-staging"
  private_key = acme_certificate.rockstart_project_static_website_staging.private_key_pem
  certificate = acme_certificate.rockstart_project_static_website_staging.certificate_pem
}

# Use Google CloudDNS to solve the DNS-01 challenge
resource "acme_certificate" "rockstart-project-static-website-dev" {
  account_key_pem = acme_registration.website.account_key_pem
  common_name     = "dev.${var.domain}"

  dns_challenge {
    provider = "gcloud"

    config = {
      GCE_PROJECT              = var.project_id
      GCE_SERVICE_ACCOUNT_FILE = var.service_account_key_file
    }
  }
}

# Assign the certificates to the HTTPS proxy of the load balancer, not needed for HTTP-only proxy
resource "google_compute_target_https_proxy" "websites" {
  name    = "websites"
  url_map = google_compute_url_map.websites.self_link

  ssl_certificates = [
    google_compute_ssl_certificate.rockstart_project_static_website_dev.id,
    google_compute_ssl_certificate.rockstart_project_static_website_staging.id,
  ]
}

# Map the incoming traffic of the load balancer to the HTTPS (or HTTP) proxy
resource "google_compute_global_forwarding_rule" "websites" {
  name                  = "websites"
  load_balancing_scheme = "EXTERNAL"
  target                = google_compute_target_https_proxy.websites.self_link
  ip_address            = google_compute_global_address.loadbalancer.address
  port_range            = "443"
}
```

But hold on, the best part isn’t just the ease. It’s the cost, my friends! Hosting on a storage bucket is like staying at a budget hostel compared to the five-star penthouse suite of Kubernetes. Your wallet will thank you, and you can use the savings to buy… well, more pizza, naturally.

Now, if you’ll excuse me, I have a date with a bucket full of static bliss and a load balancer just raring to go. Cheers to simple solutions and delicious pizza!

And for the Kubernetes diehards, don’t fret! There are plenty of complex challenges out there for your talents. Leave the static sites to us bucket-wielding code cowboys, and together, we’ll keep the web spinning beautifully.
