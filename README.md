# awesome-x402-mcp-services

Curated list of **hosted MCP services** that charge per call with [x402](https://www.x402.org) — no account, no API key. Same shape as Recall Kitchen: a real product you can point an MCP client at and pay USDC when a tool needs it.

This is not a directory of crypto trading bots, token screens, payment routers, or 50-tool utility packs.

### Table of Contents

* [Search](#search)
* [News](#news)
* [Web access](#web-access)
* [Business data](#business-data)
* [Marketing](#marketing)
* [Monitoring](#monitoring)
* [Official records](#official-records)
* [Data utilities](#data-utilities)

### Helpful Links

* [x402/MCP Developers](#developers)

## Services

### Search

* [Recall Kitchen](https://recallkitchen.com/docs/#mcp) — product, food, and vehicle recall search. Hosted MCP, x402 USDC on Base, no account. [MCP](https://app.recallkitchen.com/mcp)

### News

* [askzephy news feed](https://audit.askzephy.com) — Google News coverage per topic query (brand, competitor, person, exact phrase): normalized rows with title, article URL, publisher, publish time, snippet; 10 locales. Hosted MCP at `https://audit.askzephy.com/mcp` (streamable HTTP, no key). Unpaid `google_news_feed` calls return HTTP 402 (USDC on Base, $0.01); sign the payment and retry in the call `_meta`.
* [Briefing Service](https://wholemind.tech/briefing/index.html) — hourly LLM-ranked news briefings (AI, frontier labs, markets, US and world news, sports) from ~100 feeds: lead, why-it-matters, key points and duplicates merged, as JSON or rendered e-ink pages. Hosted MCP at `https://briefing-service.wholemind.workers.dev/mcp` (streamable HTTP, no key); `initialize` and `tools/list` are free, `tools/call` returns the x402 challenge (USDC on Base, $0.005 per call) after a small free daily quota.

### Web access

* [CyberWareX Agent Web-Access](https://web.cyberwarex.com) — live web pages for agents: JS-rendered fetch to markdown/text/html, CSS-selector extract, screenshot, PDF. Hosted MCP at `https://web.cyberwarex.com/mcp` (streamable HTTP, no key). Unpaid tool calls return the x402 invoice (USDC on Base, $0.002-0.005); pay and retry with `x_payment`.

### Business data

* [AgentPay](https://agentpay.help) — Insurance lead and business-document analysis: classify an inbound insurance lead, extract structured fields from the form or email, and return a claims/lead summary. Hosted MCP at `https://agentpay.help/mcp` (streamable HTTP, no key); unpaid tool calls return HTTP 402 (USDC on Base, $0.005-$0.10). JSON in, JSON out.
* [Saymon RU Data API](https://payforapi.com) — Russian company registry (EGRUL) and KYB dossiers, official Russian series (Central Bank rates, MOEX quotes), and Runet search. Hosted MCP at `https://payforapi.com/mcp`. Unpaid tool calls return an x402 payment error (USDC on Base, $0.005-$0.05); sign the payment and retry in the call `_meta`.
* [Sirenic](https://api.sirenic.eu) — French and European company registry: search, profiles, KYB, sanctions, filed financials. Hosted MCP at `https://api.sirenic.eu/mcp`. Unpaid calls return HTTP 402 (USDC/EURC on Base).

### Marketing

* [Social Intel](https://socialintel.dev) — Instagram influencer search by niche, country, city, and follower count. Hosted MCP at `https://socialintel.dev/mcp`. Paid `search_leads` via x402; `demo=true` is free.

### Monitoring

* [Longwatch](https://longwatch.dev) — durable watches on public pages, RSS, and SEC EDGAR filings, with resumable cursors. Hosted MCP at `https://longwatch.dev/mcp`. Paid tools return HTTP 402 (USDC on Base); free demo at `/demo`.

### Official records

* [Agent402 SEC Filings](https://agent402.tools/mcp/sec) — SEC EDGAR over MCP: company lookup, filings, full-text search, Form 4 insider trades, 13F holdings and XBRL financials, plus grounded filing, insider and fund reports. Hosted MCP at `https://agent402.tools/mcp/sec`; every tool is paid per call in USDC and an unpaid call answers a payment challenge.
* [Truth Bear (GAUGE)](https://api.truthbear.co) — official-series records (FRED, USGS, SEC EDGAR, NOAA, EPA, and similar) with a source URL and a recomputable record hash. Hosted MCP at `https://api.truthbear.co/mcp`. Coverage tools are free; paid records go through an x402 challenge.

### Data utilities

* [Penniless Data Utilities](https://penniless-json-repair.sjaman.workers.dev) — nine deterministic AI-agent data and lookup utilities (JSON repair, YAML→JSON, cron next-run, text diff/extract, HTML→text, RDAP WHOIS, DNS-over-HTTPS, GitHub repo stats, email validation). Hosted MCP at `https://penniless-json-repair.sjaman.workers.dev/mcp` (streamable HTTP, no key); `tools/list` is free. Each tool is $0.001 USDC per call on Base via x402 v2; an unpaid `tools/call` returns a 402 payment-required error whose data carries the signed-payment requirements. Pay and retry with the payment in the call `_meta`. [Discovery](https://penniless-json-repair.sjaman.workers.dev/.well-known/agent.json)

* [Crypto-Bot Honesty Audit](class URL {
  #context;
  #searchParams;
  #searchParamsModified;

  static {
    isURLInstance = (value) => typeof value === 'object' && value !== null && #context in value;

    getURLHref = (value) => {
      value.#ensureSearchParamsUpdated();
      return value.#context.href;
    };

    setURLSearchParamsModified = (obj) => {
      // When URLSearchParams changes, we lazily update URL on the next read/write for performance.
      obj.#searchParamsModified = true;

      // If URL has an existing search, remove it without cascading back to URLSearchParams.
      // Do this to avoid any internal confusion about whether URLSearchParams or URL is up-to-date.
      if (obj.#context.hasSearch) {
        obj.#updateContext(bindingUrl.update(obj.#context.href, updateActions.kSearch, ''));
      }
    };
  }

  constructor(input, base = undefined, parseSymbol = undefined) {
    if (arguments.length === 0) {
      throw new ERR_MISSING_ARGS('url');
    }

    // StringPrototypeToWellFormed is not needed.
    if (typeof input !== 'string') {
      input = `${input}`;
    }

    if (base !== undefined && typeof base !== 'string') {
      base = `${base}`;
    }

    let href;
    if (arguments.length < 3) {
      href = bindingUrl.parse(input, base, true);
    } else {
      const raiseException = parseSymbol !== kParseURLSymbol;
      const interpretAsWindowsPath = parseSymbol === kCreateURLFromWindowsPathSymbol;
      const pathToFileURL = interpretAsWindowsPath || (parseSymbol === kCreateURLFromPosixPathSymbol);
      href = pathToFileURL ?
        bindingUrl.pathToFileURL(input, interpretAsWindowsPath, base) :
        bindingUrl.parse(input, base, raiseException);
    }

    // Delay context allocation until parse finishes so invalid URLs that
    // throw do not pay for an unused URLContext. Initialize in one shot
    // from the binding snapshot instead of writing an empty context first.
    this.#context = href ? new URLContext(href) : new URLContext();
    markTransferMode(this, false, false);
  }

  static parse(input, base = undefined) {
    if (arguments.length === 0) {
      throw new ERR_MISSING_ARGS('url');
    }
    const parsedURLObject = new URL(input, base, kParseURLSymbol);
    return parsedURLObject.href ? parsedURLObject : null;
  }

  [inspect.custom](depth, opts) {
    if (typeof depth === 'number' && depth < 0)
      return this;

    const constructor = getConstructorOf(this) || URL;
    const obj = { __proto__: { constructor } };

    obj.href = this.href;
    obj.origin = this.origin;
    obj.protocol = this.protocol;
    obj.username = this.username;
    obj.password = this.password;
    obj.host = this.host;
    obj.hostname = this.hostname;
    obj.port = this.port;
    obj.pathname = this.pathname;
    obj.search = this.search;
    obj.searchParams = this.searchParams;
    obj.hash = this.hash;

    if (opts.showHidden) {
      obj[contextForInspect] = this.#context;
    }

    return `${constructor.name} ${inspect(obj, opts)}`;
  }

  #getSearchFromContext() {
    if (!this.#context.hasSearch) return '';
    let endsAt = this.#context.href.length;
    if (this.#context.hasHash) endsAt = this.#context.hash_start;
    if (endsAt - this.#context.search_start <= 1) return '';
    return StringPrototypeSlice(this.#context.href, this.#context.search_start, endsAt);
  }

  #getSearchFromParams() {
    if (!this.#searchParams?.size) return '';
    return `?${this.#searchParams}`;
  }

  #ensureSearchParamsUpdated() {
    // URL is updated lazily to greatly improve performance when URLSearchParams is updated repeatedly.
    // If URLSearchParams has been modified, reflect that back into URL, without cascading back.
    if (this.#searchParamsModified) {
      this.#searchParamsModified = false;
      this.#updateContext(bindingUrl.update(this.#context.href, updateActions.kSearch, this.#getSearchFromParams()));
    }
  }

  /**
   * Update the internal context state for URL.
   * @param {string} href New href string from `bindingUrl.update`.
   * @param {boolean} [shouldUpdateSearchParams] If the update has potential to update search params (href/search).
   */
  #updateContext(href, shouldUpdateSearchParams = false) {
    const previousSearch = shouldUpdateSearchParams && this.#searchParams &&
      (this.#searchParamsModified ? this.#getSearchFromParams() : this.#getSearchFromContext());

    setURLContextFromBinding(this.#context, href);

    if (this.#searchParams) {
      // If the search string has updated, URL becomes the source of truth, and we update URLSearchParams.
      // Only do this when we're expecting it to have changed, otherwise a change to hash etc.
      // would incorrectly compare the URLSearchParams state to the empty URL search state.
      if (shouldUpdateSearchParams) {
        const currentSearch = this.#getSearchFromContext();
        if (previousSearch !== currentSearch) {
          setURLSearchParams(this.#searchParams, currentSearch);
          this.#searchParamsModified = false;
        }
      }

      // If we have a URLSearchParams, ensure that URL is up-to-date with any modification to it.
      this.#ensureSearchParamsUpdated();
    }
  }

  toString() {
    // Updates to URLSearchParams are lazily propagated to URL, so we need to check we're in sync.
    this.#ensureSearchParamsUpdated();
    return this.#context.href;
  }

  get href() {
    // Updates to URLSearchParams are lazily propagated to URL, so we need to check we're in sync.
    this.#ensureSearchParamsUpdated();
    return this.#context.href;
  }

  set href(value) {
    value = `${value}`;
    const href = bindingUrl.update(this.#context.href, updateActions.kHref, value);
    if (!href) { throw new ERR_INVALID_URL(value); }
    this.#updateContext(href, true);
  }

  // readonly
  get origin() {
    const protocol = StringPrototypeSlice(this.#context.href, 0, this.#context.protocol_end);

    // Check if scheme_type is not `NOT_SPECIAL`
    if (this.#context.scheme_type !== 1) {
      // Check if scheme_type is `FILE`
      if (this.#context.scheme_type === 6) {
        return 'null';
      }
      return `${protocol}//${this.host}`;
    }

    if (protocol === 'blob:') {
      const path = this.pathname;
      if (path.length > 0) {
        try {
          const out = new URL(path);
          // Only return origin of scheme is `http` or `https`
          // Otherwise return a new opaque origin (null).
          if (out.#context.scheme_type === 0 || out.#context.scheme_type === 2) {
            return `${out.protocol}//${out.host}`;
          }
        } catch {
          // Do nothing.
        }
      }
    }

    return 'null';
  }

  get protocol() {
    return StringPrototypeSlice(this.#context.href, 0, this.#context.protocol_end);
  }

  set protocol(value) {
    const href = bindingUrl.update(this.#context.href, updateActions.kProtocol, `${value}`);
    if (href) {
      this.#updateContext(href);
    }
  }

  get username() {
    if (this.#context.protocol_end + 2 < this.#context.username_end) {
      return StringPrototypeSlice(this.#context.href, this.#context.protocol_end + 2, this.#context.username_end);
    }
    return '';
  }

  set username(value) {
    const href = bindingUrl.update(this.#context.href, updateActions.kUsername, `${value}`);
    if (href) {
      this.#updateContext(href);
    }
  }

  get password() {
    if (this.#context.host_start - this.#context.username_end > 0) {
      return StringPrototypeSlice(this.#context.href, this.#context.username_end + 1, this.#context.host_start);
    }
    return '';
  }

  set password(value) {
    const href = bindingUrl.update(this.#context.href, updateActions.kPassword, `${value}`);
    if (href) {
      this.#updateContext(href);
    }
  }

  get host() {
    let startsAt = this.#context.host_start;
    if (this.#context.href[startsAt] === '@') {
      startsAt++;
    }
    // If we have an empty host, then the space between components.host_end and
    // components.pathname_start may be occupied by /.
    if (startsAt === this.#context.host_end) {
      return '';
    }
    return StringPrototypeSlice(this.#context.href, startsAt, this.#context.pathname_start);
  }

  set host(value) {
    const href = bindingUrl.update(this.#context.href, updateActions.kHost, `${value}`);
    if (href) {
      this.#updateContext(href);
    }
  }

  get hostname() {
    let startsAt = this.#context.host_start;
    // host_start might be "@" if the URL has credentials
    if (this.#context.href[startsAt] === '@') {
      startsAt++;
    }
    return StringPrototypeSlice(this.#context.href, startsAt, this.#context.host_end);
  }

  set hostname(value) {
    const href = bindingUrl.update(this.#context.href, updateActions.kHostname, `${value}`);
    if (href) {
      this.#updateContext(href);
    }
  }

  get port() {
    if (this.#context.hasPort) {
      return `${this.#context.port}`;
    }
    return '';
  }

  set port(value) {
    const href = bindingUrl.update(this.#context.href, updateActions.kPort, `${value}`);
    if (href) {
      this.#updateContext(href);
    }
  }

  get pathname() {
    let endsAt;
    if (this.#context.hasSearch) {
      endsAt = this.#context.search_start;
    } else if (this.#context.hasHash) {
      endsAt = this.#context.hash_start;
    }
    return StringPrototypeSlice(this.#context.href, this.#context.pathname_start, endsAt);
  }

  set pathname(value) {
    const href = bindingUrl.update(this.#context.href, updateActions.kPathname, `${value}`);
    if (href) {
      this.#updateContext(href);
    }
  }

  get search() {
    // Updates to URLSearchParams are lazily propagated to URL, so we need to check we're in sync.
    this.#ensureSearchParamsUpdated();
    return this.#getSearchFromContext();
  }

  set search(value) {
    const href = bindingUrl.update(this.#context.href, updateActions.kSearch, StringPrototypeToWellFormed(`${value}`));
    if (href) {
      this.#updateContext(href, true);
    }
  }

  // readonly
  get searchParams() {
    // Create URLSearchParams on demand to greatly improve the URL performance.
    if (this.#searchParams == null) {
      this.#searchParams = new URLSearchParams(this.#getSearchFromContext());
      setURLSearchParamsContext(this.#searchParams, this);
      this.#searchParamsModified = false;
    }
    return this.#searchParams;
  }

  get hash() {
    if (!this.#context.hasHash || (this.#context.href.length - this.#context.hash_start <= 1)) {
      return '';
    }
    return StringPrototypeSlice(this.#context.href, this.#context.hash_start);
  }

  set hash(value) {
    const href = bindingUrl.update(this.#context.href, updateActions.kHash, `${value}`);
    if (href) {
      this.#updateContext(href);
    }
  }

  toJSON() {
    // Updates to URLSearchParams are lazily propagated to URL, so we need to check we're in sync.
    this.#ensureSearchParamsUpdated();
    return this.#context.href;
  }

  static canParse(url, base = undefined) {
    if (arguments.length === 0) {
      throw new ERR_MISSING_ARGS('url');
    }

    if (typeof url !== 'string') {
      url = `${url}`;
    }

    if (base !== undefined) {
      return bindingUrl.canParse(url, typeof base === 'string' ? base : `${base}`);
    }

    // It is important to differentiate the canParse call statements
    // since they resolve into different v8 fast api overloads.
    return bindingUrl.canParse(url);
  }
}) — scans one JS/TS crypto-bot source file for the bug patterns that make a bot report income it never earned (testnet-as-USD, silent-zero balance, cooldown key mismatch, fake faucet endpoints, speculative earnings text). Hosted MCP at `class URL {
  #context;
  #searchParams;
  #searchParamsModified;

  static {
    isURLInstance = (value) => typeof value === 'object' && value !== null && #context in value;

    getURLHref = (value) => {
      value.#ensureSearchParamsUpdated();
      return value.#context.href;
    };

    setURLSearchParamsModified = (obj) => {
      // When URLSearchParams changes, we lazily update URL on the next read/write for performance.
      obj.#searchParamsModified = true;

      // If URL has an existing search, remove it without cascading back to URLSearchParams.
      // Do this to avoid any internal confusion about whether URLSearchParams or URL is up-to-date.
      if (obj.#context.hasSearch) {
        obj.#updateContext(bindingUrl.update(obj.#context.href, updateActions.kSearch, ''));
      }
    };
  }

  constructor(input, base = undefined, parseSymbol = undefined) {
    if (arguments.length === 0) {
      throw new ERR_MISSING_ARGS('url');
    }

    // StringPrototypeToWellFormed is not needed.
    if (typeof input !== 'string') {
      input = `${input}`;
    }

    if (base !== undefined && typeof base !== 'string') {
      base = `${base}`;
    }

    let href;
    if (arguments.length < 3) {
      href = bindingUrl.parse(input, base, true);
    } else {
      const raiseException = parseSymbol !== kParseURLSymbol;
      const interpretAsWindowsPath = parseSymbol === kCreateURLFromWindowsPathSymbol;
      const pathToFileURL = interpretAsWindowsPath || (parseSymbol === kCreateURLFromPosixPathSymbol);
      href = pathToFileURL ?
        bindingUrl.pathToFileURL(input, interpretAsWindowsPath, base) :
        bindingUrl.parse(input, base, raiseException);
    }

    // Delay context allocation until parse finishes so invalid URLs that
    // throw do not pay for an unused URLContext. Initialize in one shot
    // from the binding snapshot instead of writing an empty context first.
    this.#context = href ? new URLContext(href) : new URLContext();
    markTransferMode(this, false, false);
  }

  static parse(input, base = undefined) {
    if (arguments.length === 0) {
      throw new ERR_MISSING_ARGS('url');
    }
    const parsedURLObject = new URL(input, base, kParseURLSymbol);
    return parsedURLObject.href ? parsedURLObject : null;
  }

  [inspect.custom](depth, opts) {
    if (typeof depth === 'number' && depth < 0)
      return this;

    const constructor = getConstructorOf(this) || URL;
    const obj = { __proto__: { constructor } };

    obj.href = this.href;
    obj.origin = this.origin;
    obj.protocol = this.protocol;
    obj.username = this.username;
    obj.password = this.password;
    obj.host = this.host;
    obj.hostname = this.hostname;
    obj.port = this.port;
    obj.pathname = this.pathname;
    obj.search = this.search;
    obj.searchParams = this.searchParams;
    obj.hash = this.hash;

    if (opts.showHidden) {
      obj[contextForInspect] = this.#context;
    }

    return `${constructor.name} ${inspect(obj, opts)}`;
  }

  #getSearchFromContext() {
    if (!this.#context.hasSearch) return '';
    let endsAt = this.#context.href.length;
    if (this.#context.hasHash) endsAt = this.#context.hash_start;
    if (endsAt - this.#context.search_start <= 1) return '';
    return StringPrototypeSlice(this.#context.href, this.#context.search_start, endsAt);
  }

  #getSearchFromParams() {
    if (!this.#searchParams?.size) return '';
    return `?${this.#searchParams}`;
  }

  #ensureSearchParamsUpdated() {
    // URL is updated lazily to greatly improve performance when URLSearchParams is updated repeatedly.
    // If URLSearchParams has been modified, reflect that back into URL, without cascading back.
    if (this.#searchParamsModified) {
      this.#searchParamsModified = false;
      this.#updateContext(bindingUrl.update(this.#context.href, updateActions.kSearch, this.#getSearchFromParams()));
    }
  }

  /**
   * Update the internal context state for URL.
   * @param {string} href New href string from `bindingUrl.update`.
   * @param {boolean} [shouldUpdateSearchParams] If the update has potential to update search params (href/search).
   */
  #updateContext(href, shouldUpdateSearchParams = false) {
    const previousSearch = shouldUpdateSearchParams && this.#searchParams &&
      (this.#searchParamsModified ? this.#getSearchFromParams() : this.#getSearchFromContext());

    setURLContextFromBinding(this.#context, href);

    if (this.#searchParams) {
      // If the search string has updated, URL becomes the source of truth, and we update URLSearchParams.
      // Only do this when we're expecting it to have changed, otherwise a change to hash etc.
      // would incorrectly compare the URLSearchParams state to the empty URL search state.
      if (shouldUpdateSearchParams) {
        const currentSearch = this.#getSearchFromContext();
        if (previousSearch !== currentSearch) {
          setURLSearchParams(this.#searchParams, currentSearch);
          this.#searchParamsModified = false;
        }
      }

      // If we have a URLSearchParams, ensure that URL is up-to-date with any modification to it.
      this.#ensureSearchParamsUpdated();
    }
  }

  toString() {
    // Updates to URLSearchParams are lazily propagated to URL, so we need to check we're in sync.
    this.#ensureSearchParamsUpdated();
    return this.#context.href;
  }

  get href() {
    // Updates to URLSearchParams are lazily propagated to URL, so we need to check we're in sync.
    this.#ensureSearchParamsUpdated();
    return this.#context.href;
  }

  set href(value) {
    value = `${value}`;
    const href = bindingUrl.update(this.#context.href, updateActions.kHref, value);
    if (!href) { throw new ERR_INVALID_URL(value); }
    this.#updateContext(href, true);
  }

  // readonly
  get origin() {
    const protocol = StringPrototypeSlice(this.#context.href, 0, this.#context.protocol_end);

    // Check if scheme_type is not `NOT_SPECIAL`
    if (this.#context.scheme_type !== 1) {
      // Check if scheme_type is `FILE`
      if (this.#context.scheme_type === 6) {
        return 'null';
      }
      return `${protocol}//${this.host}`;
    }

    if (protocol === 'blob:') {
      const path = this.pathname;
      if (path.length > 0) {
        try {
          const out = new URL(path);
          // Only return origin of scheme is `http` or `https`
          // Otherwise return a new opaque origin (null).
          if (out.#context.scheme_type === 0 || out.#context.scheme_type === 2) {
            return `${out.protocol}//${out.host}`;
          }
        } catch {
          // Do nothing.
        }
      }
    }

    return 'null';
  }

  get protocol() {
    return StringPrototypeSlice(this.#context.href, 0, this.#context.protocol_end);
  }

  set protocol(value) {
    const href = bindingUrl.update(this.#context.href, updateActions.kProtocol, `${value}`);
    if (href) {
      this.#updateContext(href);
    }
  }

  get username() {
    if (this.#context.protocol_end + 2 < this.#context.username_end) {
      return StringPrototypeSlice(this.#context.href, this.#context.protocol_end + 2, this.#context.username_end);
    }
    return '';
  }

  set username(value) {
    const href = bindingUrl.update(this.#context.href, updateActions.kUsername, `${value}`);
    if (href) {
      this.#updateContext(href);
    }
  }

  get password() {
    if (this.#context.host_start - this.#context.username_end > 0) {
      return StringPrototypeSlice(this.#context.href, this.#context.username_end + 1, this.#context.host_start);
    }
    return '';
  }

  set password(value) {
    const href = bindingUrl.update(this.#context.href, updateActions.kPassword, `${value}`);
    if (href) {
      this.#updateContext(href);
    }
  }

  get host() {
    let startsAt = this.#context.host_start;
    if (this.#context.href[startsAt] === '@') {
      startsAt++;
    }
    // If we have an empty host, then the space between components.host_end and
    // components.pathname_start may be occupied by /.
    if (startsAt === this.#context.host_end) {
      return '';
    }
    return StringPrototypeSlice(this.#context.href, startsAt, this.#context.pathname_start);
  }

  set host(value) {
    const href = bindingUrl.update(this.#context.href, updateActions.kHost, `${value}`);
    if (href) {
      this.#updateContext(href);
    }
  }

  get hostname() {
    let startsAt = this.#context.host_start;
    // host_start might be "@" if the URL has credentials
    if (this.#context.href[startsAt] === '@') {
      startsAt++;
    }
    return StringPrototypeSlice(this.#context.href, startsAt, this.#context.host_end);
  }

  set hostname(value) {
    const href = bindingUrl.update(this.#context.href, updateActions.kHostname, `${value}`);
    if (href) {
      this.#updateContext(href);
    }
  }

  get port() {
    if (this.#context.hasPort) {
      return `${this.#context.port}`;
    }
    return '';
  }

  set port(value) {
    const href = bindingUrl.update(this.#context.href, updateActions.kPort, `${value}`);
    if (href) {
      this.#updateContext(href);
    }
  }

  get pathname() {
    let endsAt;
    if (this.#context.hasSearch) {
      endsAt = this.#context.search_start;
    } else if (this.#context.hasHash) {
      endsAt = this.#context.hash_start;
    }
    return StringPrototypeSlice(this.#context.href, this.#context.pathname_start, endsAt);
  }

  set pathname(value) {
    const href = bindingUrl.update(this.#context.href, updateActions.kPathname, `${value}`);
    if (href) {
      this.#updateContext(href);
    }
  }

  get search() {
    // Updates to URLSearchParams are lazily propagated to URL, so we need to check we're in sync.
    this.#ensureSearchParamsUpdated();
    return this.#getSearchFromContext();
  }

  set search(value) {
    const href = bindingUrl.update(this.#context.href, updateActions.kSearch, StringPrototypeToWellFormed(`${value}`));
    if (href) {
      this.#updateContext(href, true);
    }
  }

  // readonly
  get searchParams() {
    // Create URLSearchParams on demand to greatly improve the URL performance.
    if (this.#searchParams == null) {
      this.#searchParams = new URLSearchParams(this.#getSearchFromContext());
      setURLSearchParamsContext(this.#searchParams, this);
      this.#searchParamsModified = false;
    }
    return this.#searchParams;
  }

  get hash() {
    if (!this.#context.hasHash || (this.#context.href.length - this.#context.hash_start <= 1)) {
      return '';
    }
    return StringPrototypeSlice(this.#context.href, this.#context.hash_start);
  }

  set hash(value) {
    const href = bindingUrl.update(this.#context.href, updateActions.kHash, `${value}`);
    if (href) {
      this.#updateContext(href);
    }
  }

  toJSON() {
    // Updates to URLSearchParams are lazily propagated to URL, so we need to check we're in sync.
    this.#ensureSearchParamsUpdated();
    return this.#context.href;
  }

  static canParse(url, base = undefined) {
    if (arguments.length === 0) {
      throw new ERR_MISSING_ARGS('url');
    }

    if (typeof url !== 'string') {
      url = `${url}`;
    }

    if (base !== undefined) {
      return bindingUrl.canParse(url, typeof base === 'string' ? base : `${base}`);
    }

    // It is important to differentiate the canParse call statements
    // since they resolve into different v8 fast api overloads.
    return bindingUrl.canParse(url);
  }
}/mcp` (streamable HTTP, no key); `initialize` and `tools/list` are free, an unpaid `tools/call` returns the x402 challenge (USDC on Base, $0.05 per call). Source: https://github.com/kaminariouji/x402-audit-agent

## Developers

* [xpaysh/awesome-x402](https://github.com/xpaysh/awesome-x402)
  * [Quick Start Guides](https://github.com/xpaysh/awesome-x402?tab=readme-ov-file#-quickstart-guides)
  * [Example Applications](https://github.com/xpaysh/awesome-x402?tab=readme-ov-file#-example-applications)
* [xpaysh/awesome-mcp-monetization](https://github.com/xpaysh/awesome-mcp-monetization)
* [xpaysh/awesome-agentic-economy](https://github.com/xpaysh/awesome-agentic-economy)

## Contributing

To add a commercial service (no payment required for submission):

1. **Hosted MCP** — a public streamable-HTTP MCP URL that answers `initialize` / `tools/list` without an API key.
2. **x402** — a paid tool or route returns HTTP 402 (or an MCP payment error with x402 accepts). No account required.
3. **A real product** — one job an agent would hire you for (search, registry data, filings, monitoring, and so on). Deployed today, not a Cloudflare tunnel, `localhost`, Tailscale, or `nip.io` IP.
4. **Not a fit** — crypto/trading/DeFi/token screens, generic 20–500 tool dumps, x402 routers/marketplaces/facilitators, games, or SDKs. Put SDKs and awesome-lists under Developers only if they are documentation, not a self-listing.

Open a pull request that adds one bullet under the matching Services section:

`* [Service Name](https://link-to-service) — Brief description.`

Keep sections alphabetized by service name. In the PR body, include the MCP URL and how an unpaid call produces 402. We will probe those before merging.
