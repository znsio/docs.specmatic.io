---
title: Specmatic Documentation
layout: default
nav_order: 1
redirect_from:
  - /documentation
---

<div class="container">
  <div class="row">
    <div class="col-md-6 text-center">
      <img src="{{ "/images/specmatic-logo.png" | relative_url }}" alt="Specmatic logo">
    </div>
    <div class="col-md-6">
      <h1 class="header-light regular-pad">What is Specmatic?</h1>
      <p class="lead">
        Specmatic is a tool that transforms API specifications into executable contracts, enabling <a
          href="/contract_driven_development.html">Contract-Driven Development (CDD)</a>.
        It allows teams to confidently
        develop and deploy microservices and microfrontends faster by leveraging API specifications like OpenAPI,
        AsyncAPI, GraphQL, and gRPC Proto files.
      </p>
      <p>
        It helps ensure that dependencies between services are explicitly defined, reducing the need for fragile
        integration tests and allowing independent deployments.
        There's also Specmatic Insights, which provides visibility into microservices, tracks dependencies, and helps
        teams spot potential issues early
      </p>
    </div>
  </div>
  <div class="grid-container">
    <div class="grid-item">
      <h2>Get started</h2>
      <ul>
        <li><a href="/get-started/run-your-first-contract-test">Run your first contract test</a></li>
        <li><a href="/get-started/concepts">Understand concepts in Specmatic</a></li>
      </ul>
    </div>
    <div class="grid-item">
      <h2>Next steps</h2>
      <ul>
        <li><a href="/next-steps/test-backward-compatibility">Test backward compatibility</a></li>
        <li><a href="/next-steps/ci-cd-integration">CI/CD integration.</a></li>
      </ul>
    </div>
    <div class="grid-item">
      <h2>Supported interactions</h2>
      <ul>
        <li><a href="/addons/asyncapi">AsyncAPI</a></li>
        <li><a href="/addons/google-pub-sub">Google Pub/Sub</a></li>
        <li><a href="/addons/GraphQL">GraphQL</a></li>
        <li><a href="/addons/gRPC">gRPC</a></li>
        <li><a href="/addons/jdbc">JDBC</a></li>
        <li><a href="/addons/kafka">Kafka</a></li>
        <li><a href="/addons/openapi">OpenAPI</a></li>
        <li><a href="/addons/redis">redis</a></li>
        <li><a href="/addons/SOAP">SOAP</a></li>
      </ul>
    </div>
  </div>
</div>
