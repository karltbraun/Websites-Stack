# Review of current state and desired future state of the project

## Project Overview

This repository is the shared infrastructure layer for five websites operated under KTBCS. It contains the nginx virtual host configuration and Portainer stack file that serve all sites from a single nginx container on a Vultr VM.

Each site lives in its own subdirectory and its own independent git repo. This repo tracks only the shared infrastructure files.

The hosting provider is Vultr, and the VM is running Ubuntu 22.04. The nginx container is running on port 80 and 443, and is reverse proxying to the other containers for each website.

## How we got here

We started with some of these web sites being set up separately, and gradually merged them together.  Here's an overview of what these websites are:

### ktbcs - ktbcs.xyz

This is a mostly static website which holds my CV for professional reasons.  It's essentially an on line business card with background information about me and my work experience.  It has a certficate, and we need to make sure it stays active and up to date.

### karltbraun - karltbraun.net

This is intended to be a more personal website, with a blog and other content.  It is currently not active. I don't anticipate this site having a certificate anytime soon, but it should be set up in a way that allows for easy addition of a certificate in the future.

### skinnerbraun - skinnerbraun.com

This is a website for the family business; not active yet but reserved for the future.  It should be set up in a way that allows for easy addition of a certificate in the future. Note that we have skinnerbraun.com (currently DNS managed at Cloudfare and registered with GoDaddy) and skinnerbraun.xyz (which would be for any lab work or dev/test environment).  This is also DNS managed by Cloudfare but registered with Namecheap.com.  Neither are active, meaning there is no website configuration for it yet (or at least, no complete website configuration).  No plans to make it active in the near future.

### skinnereditorial - skinnereditorial.com

This is my wife's website for her editorial business.  It is currently in a mock-up state, with lots of work to be done pending development of her business. We want to keep the certificate active no this so she can use it as an online business card and to share with potential clients.

### SkinnerWilliamsBraun - SkinnerWilliamsBraun.net

This is a website for coordinating events with the larger extended family.  It is currently active, and we want to keep the certificate active.

## Current State

We have just moved all of these websites into a single project.  I think (but am not sure) that nginx drives all of these websites.  
We know certificates for ktbcs.xyz and skinnereditorial.com have expired and need to be renewed.  We should make sure that these, and the certificate for SkinnerWilliamsBraun.net are set up to renew in the most automated way possible.

## Desired Future State

(May be current state, or partially implemented).  We want each website to be in it's own container, with nginx also sitting in its own container and reverse proxying to the other containers.  This will allow us to have more flexibility in how we manage each website, and will also make it easier to add new websites in the future.  We also want to make sure that each website has its own certificate, so that we can keep them all active and secure.
