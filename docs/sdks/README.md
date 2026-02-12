---
title: SDKs
description: Official client libraries for MUXI
---
# SDKs

## Official client libraries for MUXI

Native SDKs for 12 languages. Build custom UIs, manage user credentials, handle observability events, and control formations programmatically.

## Why Use the SDKs?

The SDKs are how developers build products on top of MUXI:

| Use Case | What SDKs Enable |
|----------|------------------|
| **Custom Chat UIs** | Build your own chat interface instead of using the default |
| **Credential Management** | Let users add API keys/tokens via your UI (not via chat) |
| **Observability Dashboards** | Subscribe to 350+ event types for monitoring and debugging |
| **User Management** | Manage scheduled tasks, memory, and sessions per user |
| **Formation Control** | Deploy, start, stop, restart formations programmatically |

> [!TIP]
> **Security note:** When user credentials are required for tools (GitHub, Gmail, etc.), developers can configure whether users provide them via chat or via a dedicated credentials page. The SDK lets you build that credentials page.


## Quick Install


:::: cols=2

(python-sdk.md)[[card]]

<svg width="32" height="32" viewBox="0 0 32 32" xmlns="http://www.w3.org/2000/svg"><path d="m16.4 0c-1.3 0-2.2.1-3.3.3-3.2.6-3.8 1.7-3.8 3.9v3.3h7.6v1.7h-11.6c-2.2 0-4.2 1-4.8 3.6-.7 2.9-.7 4.7 0 7.7.6 2.2 1.8 3.9 4 3.9h3.1v-4.3c0-2.5 2.3-5 4.9-5h6.1c2.1 0 4.2-1.6 4.2-3.7v-7.2c0-2.1-1.5-3.6-3.6-3.9 0 0-1.5-.3-2.8-.3zm-4.2 3.4c.7 0 1.3.6 1.3 1.3s-.6 1.3-1.3 1.3-1.3-.6-1.3-1.3.6-1.3 1.3-1.3z" fill="#0277bd"/><path d="m15.6 32c1.3 0 2.2-.1 3.3-.3 3.2-.6 3.8-1.7 3.8-3.9v-3.3h-7.6v-1.7h11.5c2.2 0 4.2-1 4.8-3.6.7-2.9.7-4.7 0-7.7-.6-2.2-1.8-3.9-4-3.9h-3.1v4.3c0 2.5-2.3 5-4.9 5h-6.1c-2.1 0-4.2 1.6-4.2 3.7v7.2c0 2.1 1.5 3.6 3.6 3.9 0 0 1.5.3 2.8.3zm4.2-3.4c-.7 0-1.3-.6-1.3-1.3s.6-1.3 1.3-1.3 1.3.6 1.3 1.3-.6 1.3-1.3 1.3z" fill="#ffc107"/></svg>

#### Python

Works in scripts, servers, notebooks, and serverless. Python 3.10+

```
pip install muxi-sdk
```

**Learn more & install ›**

[[/card]]

(typescript-sdk.md)[[card]]

<svg width="32" height="32" viewBox="0 0 32 32" xmlns="http://www.w3.org/2000/svg"><rect fill="#3178c6" height="32" rx="3.1" width="32"/><rect fill="#3178c6" height="32" rx="3.1" width="32"/><path d="m19.8 25.5v3.1c.5.3 1.1.5 1.8.6s1.4.2 2.2.2 1.5 0 2.1-.2c.7-.1 1.3-.4 1.8-.7s.9-.8 1.2-1.3.4-1.2.4-2 0-1.1-.3-1.5-.4-.8-.7-1.1-.7-.6-1.1-.9-1-.5-1.5-.7c-.4-.2-.8-.3-1.1-.5s-.6-.3-.8-.5-.4-.3-.5-.5-.2-.4-.2-.6 0-.4.2-.6.3-.3.5-.4.4-.2.7-.3c.3 0 .6-.1 1-.1s.5 0 .8 0 .6 0 .9.2c.3 0 .6.2.9.3s.5.3.8.4v-2.9c-.5-.2-1-.3-1.6-.4s-1.2-.1-1.9-.1-1.4 0-2.1.2-1.3.4-1.8.7-.9.8-1.2 1.3-.4 1.2-.4 1.9.3 1.7.8 2.4 1.4 1.2 2.5 1.7c.4.2.8.3 1.2.5s.7.3 1 .5.5.4.6.6c.2.2.2.5.2.7s0 .4-.1.6-.2.3-.4.4-.4.2-.7.3c-.3 0-.6.1-1 .1-.7 0-1.3-.1-2-.4-.7-.2-1.3-.6-1.8-1.1zm-5.3-7.7h4v-2.6h-11.1v2.6h4v11.4h3.2v-11.4z" fill="#fff" fill-rule="evenodd"/></svg>

#### TypeScript

Works on your server (Node.js/Bun), in your browsers, and edge.
```
npm install @muxi/sdk
```

**Learn more & install ›**

[[/card]]

(go-sdk.md)[[card]]

<svg width="32" height="32" viewBox="0 0 32 32" xmlns="http://www.w3.org/2000/svg"><path d="m26.8 36.8-3-3-2 2 3 3c.5.5 1.4.5 2 0s.5-1.4 0-2zm2.8-16.8-3-3-2 2 3 3c.5.5 1.4.5 2 0s.5-1.4 0-2zm-24.1 16.8 3-3 2 2-3 3c-.5.5-1.4.5-2 0s-.5-1.4 0-2zm-3.1-16.8 3-3 2 2-3 3c-.5.5-1.4.5-2 0s-.5-1.4 0-2z" fill="#ffcc80"/><path d="m29.1 4.7c0-1.8-1.1-2.8-3-2.8s-3.5 2.4-3.5 4.2 1.8 1.4 2.8 1.4c1.9 0 3.7-1 3.7-2.8zm-26.2 0c0-1.8 1.1-2.8 3-2.8s3.5 2.4 3.5 4.2-1.8 1.4-2.8 1.4c-1.9 0-3.7-1-3.7-2.8z" fill="#4dd0e1"/><path d="m26.3 3.7c-.5 0-.9.4-.9.9s.4.9.9.9.9-.4.9-.9-.4-.9-.9-.9zm-20.6 0c-.5 0-.9.4-.9.9s.4.9.9.9.9-.4.9-.9-.4-.9-.9-.9z" fill="#424242"/><path d="m28.2 28.9c0 4.5-3 9.3-12.4 9.3s-11.8-4.9-11.8-9.3.9-5.4.9-9.3v-9.3c-.1-4.5 2.8-10.3 10.8-10.3s11.5 3.7 11.5 9.3-.2 5.1 0 9.3c.2 3.3.9 7.5.9 10.3z" fill="#4dd0e1"/><path d="m20.7 2.8c-2.1 0-3.7 1.7-3.7 3.7s1.7 3.7 3.7 3.7 3.7-1.7 3.7-3.7-1.7-3.7-3.7-3.7zm-9.3 0c-2.1 0-3.7 1.7-3.7 3.7s1.7 3.7 3.7 3.7 3.7-1.7 3.7-3.7-1.7-3.7-3.7-3.7z" fill="#f5f5f5"/><path d="m16 15.9c0 .5.4.9.9.9s.9-.4.9-.9v-2.8h-1.9v2.8zm-1.8 0c0 .5.4.9.9.9s.9-.4.9-.9v-2.8h-1.9v2.8z" fill="#eee"/><path d="m18.4 14c-.4 0-.6 0-.9-.2-.9-.3-1.9-.3-2.8 0-.3.1-.5.2-.9.2-1.2 0-1.4-.9-1.4-1.4 0-1.4 1.4-2.3 2.8-2.3h1.9c1.4 0 2.8.9 2.8 2.3s-.2 1.4-1.4 1.4z" fill="#ffcc80"/><path d="m18.8 5.6c-.5 0-.9.4-.9.9s.4.9.9.9.9-.4.9-.9-.4-.9-.9-.9zm-9.3 0c-.5 0-.9.4-.9.9s.4.9.9.9.9-.4.9-.9-.4-.9-.9-.9zm6.5 3.7c-1 0-1.9.4-1.9.9s.8.9 1.9.9 1.9-.4 1.9-.9-.8-.9-1.9-.9z" fill="#424242"/></svg>

#### Go

Works in services, CLIs, serverless, and embedded systems.

```
go get github.com/muxi-ai/muxi-go
```

**Learn more & install ›**

[[/card]]

[[card]]

<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none"> <path opacity="0.4" fill-rule="evenodd" clip-rule="evenodd" d="M15.9456 3.00001C15.9637 3.00001 15.9819 3.00001 16 3.00001C16.0182 3.00001 16.0363 3.00001 16.0544 3.00001C16.4785 2.99992 16.8906 2.99984 17.2305 3.04554C17.6137 3.09706 18.051 3.22259 18.4142 3.5858C18.7774 3.94902 18.903 4.38629 18.9545 4.76949C19.0002 5.10941 19.0001 5.52153 19 5.9456C19 5.96372 19 5.98186 19 6.00001V8.00001C19 8.01817 19 8.03631 19 8.05442C19.0001 8.47849 19.0002 8.89062 18.9545 9.23053C18.903 9.61374 18.7774 10.051 18.4142 10.4142C18.051 10.7774 17.6137 10.903 17.2305 10.9545C16.8906 11.0002 16.4785 11.0001 16.0544 11C16.0363 11 16.0182 11 16 11C15.9819 11 15.9637 11 15.9456 11C15.5215 11.0001 15.1094 11.0002 14.7695 10.9545C14.3863 10.903 13.949 10.7774 13.5858 10.4142C13.2226 10.051 13.0971 9.61374 13.0455 9.23053C12.9998 8.89062 12.9999 8.47849 13 8.05442C13 8.03631 13 8.01817 13 8.00001V6.00001C13 5.98186 13 5.96372 13 5.94561C12.9999 5.52154 12.9998 5.10941 13.0455 4.76949C13.0971 4.38629 13.2226 3.94902 13.5858 3.5858C13.949 3.22259 14.3863 3.09706 14.7695 3.04554C15.1094 2.99984 15.5215 2.99992 15.9456 3.00001ZM15.3999 5.00001C15.2114 5.00001 15.1172 5 15.0586 5.05858C15 5.11716 15 5.21139 15 5.39986C15 5.57327 15 5.78553 15 6.00001V8.00001C15 8.21285 15 8.4258 15 8.60016C15 8.78862 15 8.88286 15.0586 8.94144C15.1172 9.00001 15.2114 9.00001 15.3999 9.00001C15.6624 9.00001 15.9637 9.00001 16 9.00001C16.2128 9.00001 16.4258 9.00001 16.6002 9.00001C16.7886 9.00001 16.8829 9.00001 16.9414 8.94144C17 8.88286 17 8.7886 17 8.60009C17 8.33819 17 8.03759 17 8.00001V6.00001C17 5.78553 17 5.57326 17 5.39984C17 5.21139 17 5.11716 16.9414 5.05858C16.8828 5 16.7886 5.00001 16.6002 5.00001C16.4267 5.00001 16.2145 5.00001 16 5.00001C15.7855 5.00001 15.5733 5.00001 15.3999 5.00001Z" fill="currentColor"></path> <path opacity="0.4" fill-rule="evenodd" clip-rule="evenodd" d="M3.94561 3.00001C3.96372 3.00001 3.98186 3.00001 4.00001 3.00001C4.01817 3.00001 4.03631 3.00001 4.05442 3.00001C4.47849 2.99992 4.89062 2.99984 5.23053 3.04554C5.61374 3.09706 6.05101 3.22259 6.41423 3.5858C6.77744 3.94902 6.90297 4.38629 6.95449 4.76949C7.00019 5.10941 7.0001 5.52153 7.00002 5.9456C7.00002 5.96372 7.00001 5.98186 7.00001 6.00001V8.00001C7.00001 8.01817 7.00002 8.03631 7.00002 8.05442C7.0001 8.47849 7.00019 8.89062 6.95449 9.23053C6.90297 9.61374 6.77744 10.051 6.41423 10.4142C6.05101 10.7774 5.61374 10.903 5.23053 10.9545C4.89062 11.0002 4.47849 11.0001 4.05442 11C4.03631 11 4.01817 11 4.00001 11C3.98186 11 3.96372 11 3.9456 11C3.52153 11.0001 3.10941 11.0002 2.76949 10.9545C2.38629 10.903 1.94902 10.7774 1.5858 10.4142C1.22259 10.051 1.09706 9.61374 1.04554 9.23053C0.999843 8.89062 0.999924 8.47849 1.00001 8.05442C1.00001 8.03631 1.00001 8.01817 1.00001 8.00001V6.00001C1.00001 5.98186 1.00001 5.96372 1.00001 5.94561C0.999924 5.52154 0.999843 5.10941 1.04554 4.76949C1.09706 4.38629 1.22259 3.94902 1.5858 3.5858C1.94902 3.22259 2.38629 3.09706 2.76949 3.04554C3.10941 2.99984 3.52154 2.99992 3.94561 3.00001ZM3.39987 5.00001C3.21141 5.00001 3.11717 5 3.05859 5.05858C3.00001 5.11716 3.00001 5.21139 3.00001 5.39986C3.00001 5.57327 3.00001 5.78553 3.00001 6.00001V8.00001C3.00001 8.21285 3.00001 8.4258 3.00001 8.60016C3.00001 8.78862 3.00001 8.88286 3.05859 8.94144C3.11717 9.00001 3.21143 9.00001 3.39994 9.00001C3.6624 9.00001 3.96369 9.00001 4.00001 9.00001C4.21285 9.00001 4.4258 9.00001 4.60016 9.00001C4.78862 9.00001 4.88286 9.00001 4.94144 8.94144C5.00001 8.88286 5.00001 8.7886 5.00001 8.60009C5.00001 8.33819 5.00001 8.03759 5.00001 8.00001V6.00001C5.00001 5.78553 5.00001 5.57326 5.00001 5.39984C5.00001 5.21139 5 5.11716 4.94142 5.05858C4.88284 5 4.78861 5.00001 4.60016 5.00001C4.42675 5.00001 4.21449 5.00001 4.00001 5.00001C3.78554 5.00001 3.57328 5.00001 3.39987 5.00001Z" fill="currentColor"></path> <path opacity="0.4" fill-rule="evenodd" clip-rule="evenodd" d="M8.44561 13C8.46372 13 8.48186 13 8.50001 13C8.51817 13 8.53631 13 8.55442 13C8.97849 12.9999 9.39062 12.9998 9.73053 13.0455C10.1137 13.0971 10.551 13.2226 10.9142 13.5858C11.2774 13.949 11.403 14.3863 11.4545 14.7695C11.5002 15.1094 11.5001 15.5215 11.5 15.9456C11.5 15.9637 11.5 15.9819 11.5 16V18C11.5 18.0182 11.5 18.0363 11.5 18.0544C11.5001 18.4785 11.5002 18.8906 11.4545 19.2305C11.403 19.6137 11.2774 20.051 10.9142 20.4142C10.551 20.7774 10.1137 20.903 9.73053 20.9545C9.39062 21.0002 8.97849 21.0001 8.55442 21C8.53631 21 8.51817 21 8.50001 21C8.48186 21 8.46372 21 8.4456 21C8.02153 21.0001 7.60941 21.0002 7.26949 20.9545C6.88629 20.903 6.44902 20.7774 6.0858 20.4142C5.72259 20.051 5.59706 19.6137 5.54554 19.2305C5.49984 18.8906 5.49992 18.4785 5.50001 18.0544C5.50001 18.0363 5.50001 18.0182 5.50001 18V16C5.50001 15.9819 5.50001 15.9637 5.50001 15.9456C5.49992 15.5215 5.49984 15.1094 5.54554 14.7695C5.59706 14.3863 5.72259 13.949 6.0858 13.5858C6.44902 13.2226 6.88629 13.0971 7.26949 13.0455C7.60941 12.9998 8.02154 12.9999 8.44561 13ZM7.89987 15C7.71141 15 7.61717 15 7.55859 15.0586C7.50001 15.1172 7.50001 15.2114 7.50001 15.3999C7.50001 15.5733 7.50001 15.7855 7.50001 16V18C7.50001 18.2128 7.50001 18.4258 7.50001 18.6002C7.50001 18.7886 7.50001 18.8829 7.55859 18.9414C7.61717 19 7.71143 19 7.89994 19C8.1624 19 8.46369 19 8.50001 19C8.71285 19 8.9258 19 9.10016 19C9.28862 19 9.38286 19 9.44144 18.9414C9.50001 18.8829 9.50001 18.7886 9.50001 18.6001C9.50001 18.3382 9.50001 18.0376 9.50001 18V16C9.50001 15.7855 9.50001 15.5733 9.50001 15.3998C9.50001 15.2114 9.5 15.1172 9.44142 15.0586C9.38284 15 9.28861 15 9.10016 15C8.92675 15 8.71449 15 8.50001 15C8.28554 15 8.07328 15 7.89987 15Z" fill="currentColor"></path> <path fill-rule="evenodd" clip-rule="evenodd" d="M15.4456 13C15.4637 13 15.4819 13 15.5 13C15.5182 13 15.5363 13 15.5544 13C15.9785 12.9999 16.3906 12.9998 16.7305 13.0455C17.1137 13.0971 17.551 13.2226 17.9142 13.5858C18.2774 13.949 18.403 14.3863 18.4545 14.7695C18.5002 15.1094 18.5001 15.5215 18.5 15.9456C18.5 15.9637 18.5 15.9819 18.5 16V18C18.5 18.0182 18.5 18.0363 18.5 18.0544C18.5001 18.4785 18.5002 18.8906 18.4545 19.2305C18.403 19.6137 18.2774 20.051 17.9142 20.4142C17.551 20.7774 17.1137 20.903 16.7305 20.9545C16.3906 21.0002 15.9785 21.0001 15.5544 21C15.5363 21 15.5182 21 15.5 21C15.4819 21 15.4637 21 15.4456 21C15.0215 21.0001 14.6094 21.0002 14.2695 20.9545C13.8863 20.903 13.449 20.7774 13.0858 20.4142C12.7226 20.051 12.5971 19.6137 12.5455 19.2305C12.4998 18.8906 12.4999 18.4785 12.5 18.0544C12.5 18.0363 12.5 18.0182 12.5 18V16C12.5 15.9819 12.5 15.9637 12.5 15.9456C12.4999 15.5215 12.4998 15.1094 12.5455 14.7695C12.5971 14.3863 12.7226 13.949 13.0858 13.5858C13.449 13.2226 13.8863 13.0971 14.2695 13.0455C14.6094 12.9998 15.0215 12.9999 15.4456 13ZM14.8999 15C14.7114 15 14.6172 15 14.5586 15.0586C14.5 15.1172 14.5 15.2114 14.5 15.3999C14.5 15.5733 14.5 15.7855 14.5 16V18C14.5 18.2128 14.5 18.4258 14.5 18.6002C14.5 18.7886 14.5 18.8829 14.5586 18.9414C14.6172 19 14.7114 19 14.8999 19C15.1624 19 15.4637 19 15.5 19C15.7128 19 15.9258 19 16.1002 19C16.2886 19 16.3829 19 16.4414 18.9414C16.5 18.8829 16.5 18.7886 16.5 18.6001C16.5 18.3382 16.5 18.0376 16.5 18V16C16.5 15.7855 16.5 15.5733 16.5 15.3998C16.5 15.2114 16.5 15.1172 16.4414 15.0586C16.3828 15 16.2886 15 16.1002 15C15.9267 15 15.7145 15 15.5 15C15.2855 15 15.0733 15 14.8999 15Z" fill="currentColor"></path> <path fill-rule="evenodd" clip-rule="evenodd" d="M10.972 3.11833C11.2971 3.29235 11.5001 3.63121 11.5001 4V10C11.5001 10.5523 11.0524 11 10.5001 11C9.94782 11 9.50011 10.5523 9.50011 10V5.86607C9.04713 6.12816 8.46241 5.99623 8.16806 5.5547C7.8617 5.09517 7.98588 4.4743 8.44541 4.16795L9.94541 3.16795C10.2523 2.96338 10.6468 2.94431 10.972 3.11833Z" fill="currentColor"></path> <path fill-rule="evenodd" clip-rule="evenodd" d="M3.97196 13.1183C4.29712 13.2923 4.50011 13.6312 4.50011 14V20C4.50011 20.5523 4.05239 21 3.50011 21C2.94782 21 2.50011 20.5523 2.50011 20V15.8661C2.04713 16.1282 1.46241 15.9962 1.16806 15.5547C0.861704 15.0952 0.985877 14.4743 1.44541 14.168L2.94541 13.168C3.25226 12.9634 3.64681 12.9443 3.97196 13.1183Z" fill="currentColor"></path> <path fill-rule="evenodd" clip-rule="evenodd" d="M22.472 3.11833C22.7971 3.29235 23.0001 3.63121 23.0001 4V10C23.0001 10.5523 22.5524 11 22.0001 11C21.4478 11 21.0001 10.5523 21.0001 10V5.86607C20.5471 6.12816 19.9624 5.99623 19.6681 5.5547C19.3617 5.09517 19.4859 4.4743 19.9454 4.16795L21.4454 3.16795C21.7523 2.96338 22.1468 2.94431 22.472 3.11833Z" fill="currentColor"></path> <path opacity="0.4" fill-rule="evenodd" clip-rule="evenodd" d="M22.472 13.1183C22.7971 13.2923 23.0001 13.6312 23.0001 14V20C23.0001 20.5523 22.5524 21 22.0001 21C21.4478 21 21.0001 20.5523 21.0001 20V15.8661C20.5471 16.1282 19.9624 15.9962 19.6681 15.5547C19.3617 15.0952 19.4859 14.4743 19.9454 14.168L21.4454 13.168C21.7523 12.9634 22.1468 12.9443 22.472 13.1183Z" fill="currentColor"></path> </svg>

### Also available:

<div class="grid grid-cols-2 gap-4 mt-1">
<ul>
<li><a href="/sdks/csharp-sdk.md">C#</a></li>
<li><a href="/sdks/php-sdk.md">PHP</a></li>
<li><a href="/sdks/ruby-sdk.md">Ruby</a></li>
<li><a href="/sdks/cpp-sdk.md">C/C++</a></li>
<li><a href="/sdks/java-sdk.md">Java</a></li>
</ul>
<ul>
<li><a href="/sdks/dart-sdk.md">Dart</a></li>
<li><a href="/sdks/kotlin-sdk.md">Kotlin</a></li>
<li><a href="/sdks/swift-sdk.md">Swift</a></li>
<li><a href="/sdks/rust-sdk.md">Rust</a></li>
</ul>
</div>

[[/card]]

::::


## Quick Start

[[tabs]]

[[tab Python]]
```python
from muxi import FormationClient

client = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    client_key="your_client_key",
)

for event in client.chat_stream({"message": "Hello!"}):
    if event.get("type") == "text":
        print(event.get("text"), end="")
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { FormationClient } from "@muxi-ai/muxi-typescript";

const client = new FormationClient({
  serverUrl: "http://localhost:7890",
  formationId: "my-assistant",
  clientKey: "your_client_key",
});

for await (const event of client.chatStream({ message: "Hello!" })) {
  if (event.type === "text") process.stdout.write(event.text);
}
```
[[/tab]]

[[tab Go]]
```go
client := muxi.NewFormationClient(&muxi.FormationConfig{
    ServerURL:   "http://localhost:7890",
    FormationID: "my-assistant",
    ClientKey:   "your_client_key",
})

stream, _ := client.ChatStream(ctx, &muxi.ChatRequest{Message: "Hello!"})
for chunk := range stream {
    if chunk.Type == "text" {
        fmt.Print(chunk.Text)
    }
}
```
[[/tab]]

[[tab Ruby]]
```ruby
require 'muxi'

client = Muxi::FormationClient.new(
  server_url: 'http://localhost:7890',
  formation_id: 'my-assistant',
  client_key: 'your_client_key'
)

client.chat_stream(message: 'Hello!') do |event|
  print event['text'] if event['type'] == 'text'
end
```
[[/tab]]

[[tab Java]]
```java
FormationClient client = new FormationClient(
    "http://localhost:7890",
    "my-assistant",
    "your_client_key"
);

client.chatStream(new ChatRequest("Hello!"), event -> {
    if ("text".equals(event.getType())) {
        System.out.print(event.getText());
    }
    return true;
});
```
[[/tab]]

[[tab Kotlin]]
```kotlin
val client = FormationClient(
    serverUrl = "http://localhost:7890",
    formationId = "my-assistant",
    clientKey = "your_client_key"
)

client.chatStream(ChatRequest(message = "Hello!")).collect { event ->
    if (event.type == "text") print(event.text)
}
```
[[/tab]]

[[tab Swift]]
```swift
let client = FormationClient(
    serverURL: "http://localhost:7890",
    formationID: "my-assistant",
    clientKey: "your_client_key"
)

for try await event in client.chatStream(message: "Hello!") {
    if event.type == "text" { print(event.text ?? "", terminator: "") }
}
```
[[/tab]]

[[tab C#]]
```csharp
var client = new FormationClient(
    serverUrl: "http://localhost:7890",
    formationId: "my-assistant",
    clientKey: "your_client_key"
);

await foreach (var chunk in client.ChatStreamAsync(new ChatRequest { Message = "Hello!" }))
{
    if (chunk.Type == "text") Console.Write(chunk.Text);
}
```
[[/tab]]

[[tab PHP]]
```php
$client = new FormationClient(
    serverUrl: 'http://localhost:7890',
    formationId: 'my-assistant',
    clientKey: 'your_client_key'
);

foreach ($client->chatStream(['message' => 'Hello!']) as $event) {
    if ($event['type'] === 'text') echo $event['text'];
}
```
[[/tab]]

[[tab Dart]]
```dart
final client = FormationClient(
  serverUrl: 'http://localhost:7890',
  formationId: 'my-assistant',
  clientKey: 'your_client_key',
);

await for (final event in client.chatStream(message: 'Hello!')) {
  if (event['type'] == 'text') stdout.write(event['text']);
}
```
[[/tab]]

[[tab Rust]]
```rust
let client = FormationClient::new(
    "http://localhost:7890",
    "my-assistant",
    "your_client_key",
);

let mut stream = client.chat_stream("Hello!").await?;
while let Some(event) = stream.next().await {
    if event?.event_type == "text" { print!("{}", event.text.unwrap_or_default()); }
}
```
[[/tab]]

[[tab C++]]
```cpp
auto client = muxi::FormationClient(
    "http://localhost:7890",
    "my-assistant",
    "your_client_key"
);

client.chat_stream({{"message", "Hello!"}}, [](const auto& event) {
    if (event.type == "text") std::cout << event.text;
    return true;
});
```
[[/tab]]

[[/tabs]]


## Local Development

When developing locally with `muxi up`, use the `mode="draft"` parameter to route requests through the `/draft/` endpoint on your local MUXI server. This allows you to test changes without affecting your live deployment:

[[tabs]]

[[tab Python]]
```python
client = FormationClient(
    server_url="http://localhost:7890",
    formation_id="my-assistant",
    mode="draft",  # Uses /draft/ prefix
    client_key="your_client_key",
)
```
[[/tab]]

[[tab TypeScript]]
```typescript
const client = new FormationClient({
  serverUrl: "http://localhost:7890",
  formationId: "my-assistant",
  mode: "draft",  // Uses /draft/ prefix
  clientKey: "your_client_key",
});
```
[[/tab]]

[[tab Go]]
```go
client := muxi.NewFormationClient(&muxi.FormationConfig{
    ServerURL:   "http://localhost:7890",
    FormationID: "my-assistant",
    Mode:        "draft",  // Uses /draft/ prefix
    ClientKey:   "your_client_key",
})
```
[[/tab]]

[[tab Ruby]]
```ruby
client = Muxi::FormationClient.new(
  server_url: 'http://localhost:7890',
  formation_id: 'my-assistant',
  mode: 'draft',  # Uses /draft/ prefix
  client_key: 'your_client_key'
)
```
[[/tab]]

[[tab Java]]
```java
FormationClient client = new FormationClient(
    "http://localhost:7890",
    "my-assistant",
    "your_client_key",
    "draft"  // Uses /draft/ prefix
);
```
[[/tab]]

[[tab Kotlin]]
```kotlin
val client = FormationClient(
    serverUrl = "http://localhost:7890",
    formationId = "my-assistant",
    mode = "draft",  // Uses /draft/ prefix
    clientKey = "your_client_key"
)
```
[[/tab]]

[[tab Swift]]
```swift
let client = FormationClient(
    serverURL: "http://localhost:7890",
    formationID: "my-assistant",
    mode: "draft",  // Uses /draft/ prefix
    clientKey: "your_client_key"
)
```
[[/tab]]

[[tab C#]]
```csharp
var client = new FormationClient(
    serverUrl: "http://localhost:7890",
    formationId: "my-assistant",
    mode: "draft",  // Uses /draft/ prefix
    clientKey: "your_client_key"
);
```
[[/tab]]

[[tab PHP]]
```php
$client = new FormationClient(
    serverUrl: 'http://localhost:7890',
    formationId: 'my-assistant',
    mode: 'draft',  // Uses /draft/ prefix
    clientKey: 'your_client_key'
);
```
[[/tab]]

[[tab Dart]]
```dart
final client = FormationClient(
  serverUrl: 'http://localhost:7890',
  formationId: 'my-assistant',
  mode: 'draft',  // Uses /draft/ prefix
  clientKey: 'your_client_key',
);
```
[[/tab]]

[[tab Rust]]
```rust
let client = FormationClient::new(
    "http://localhost:7890",
    "my-assistant",
    "your_client_key",
).with_mode("draft");  // Uses /draft/ prefix
```
[[/tab]]

[[tab C++]]
```cpp
auto client = muxi::FormationClient(
    "http://localhost:7890",
    "my-assistant",
    "your_client_key",
    "draft"  // Uses /draft/ prefix
);
```
[[/tab]]

[[/tabs]]

**Workflow:**
1. Run `muxi up` to start your formation in draft mode
2. Use `mode="draft"` in your SDK client during development
3. Run `muxi deploy` to deploy to production
4. Remove `mode="draft"` (or don't set it) - defaults to `mode="live"` which uses `/api/`

> [!TIP]
> The `mode` parameter only affects URL routing. All other functionality is identical between draft and live modes.


## Two Client Types

All SDKs provide two clients:

### FormationClient

For interacting with a running formation:

- Chat (streaming & non-streaming)
- Sessions & history
- Memory management
- Triggers & scheduled tasks
- Agent configuration

**Authentication:** Client key (`X-Muxi-Client-Key`) or Admin key (`X-Muxi-Admin-Key`)

> [!TIP]
> **Browser usage:** The TypeScript SDK's FormationClient works directly in browsers. Use the `clientKey` for browser apps - it's safe to expose and has limited permissions. Keep the `adminKey` server-side only.

### ServerClient

For managing the MUXI server:

- Deploy formations
- Start/stop/restart formations
- List formations
- Server health & logs

**Authentication:** HMAC signature (`key_id` + `secret_key`)


## Common Operations

### Chat (Streaming)

[[tabs]]

[[tab Python]]
```python
for event in formation.chat_stream({"message": "Hello!"}, user_id="user_123"):
    if event.get("type") == "text":
        print(event.get("text"), end="")
    elif event.get("type") == "done":
        break
```
[[/tab]]

[[tab TypeScript]]
```typescript
for await (const chunk of await formation.chatStream({ message: "Hello!" }, "user_123")) {
  if (chunk.type === "text") process.stdout.write(chunk.text);
  if (chunk.type === "done") break;
}
```
[[/tab]]

[[tab Go]]
```go
stream, errs := client.ChatStream(ctx, &muxi.ChatRequest{Message: "Hello!", UserID: "user_123"})
for chunk := range stream {
    if chunk.Type == "text" {
        fmt.Print(chunk.Text)
    }
}
```
[[/tab]]

[[/tabs]]

### Memory

[[tabs]]

[[tab Python]]
```python
# Get memories
memories = formation.get_memories(user_id="user_123")

# Add memory (user_id, mem_type, detail)
formation.add_memory(user_id="user_123", mem_type="preference", detail="User prefers Python")

# Clear buffer
formation.clear_user_buffer(user_id="user_123")
```
[[/tab]]

[[tab TypeScript]]
```typescript
// Get memories
const memories = await formation.getMemories("user_123");

// Add memory (userId, type, detail)
await formation.addMemory("user_123", "preference", "User prefers TypeScript");

// Clear buffer
await formation.clearUserBuffer("user_123");
```
[[/tab]]

[[tab Go]]
```go
// Get memories
memories, _ := client.GetMemories(ctx, "user_123")

// Add memory (ctx, userId, type, detail)
client.AddMemory(ctx, "user_123", "preference", "User prefers Go")

// Clear buffer
client.ClearUserBuffer(ctx, "user_123")
```
[[/tab]]

[[/tabs]]

### Server Management

[[tabs]]

[[tab Python]]
```python
from muxi import ServerClient

server = ServerClient(
    url="http://localhost:7890",
    key_id="muxi_pk_...",
    secret_key="muxi_sk_...",
)

# Deploy
server.deploy_formation(bundle_path="my-bot.tar.gz")

# List formations
formations = server.list_formations()

# Stop/start/restart
server.stop_formation(formation_id="my-bot")
server.start_formation(formation_id="my-bot")
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { ServerClient } from "@muxi-ai/muxi-typescript";

const server = new ServerClient({
  url: "http://localhost:7890",
  keyId: "muxi_pk_...",
  secretKey: "muxi_sk_...",
});

// Deploy
await server.deployFormation({ bundlePath: "my-bot.tar.gz" });

// List formations
const formations = await server.listFormations();

// Stop/start/restart
await server.stopFormation("my-bot");
await server.startFormation("my-bot");
```
[[/tab]]

[[tab Go]]
```go
server := muxi.NewServerClient(&muxi.ServerConfig{
    URL:       "http://localhost:7890",
    KeyID:     "muxi_pk_...",
    SecretKey: "muxi_sk_...",
})

// Deploy
server.DeployFormation(ctx, &muxi.DeployRequest{BundlePath: "my-bot.tar.gz"})

// List formations
formations, _ := server.ListFormations(ctx)

// Stop/start/restart
server.StopFormation(ctx, "my-bot")
server.StartFormation(ctx, "my-bot")
```
[[/tab]]

[[/tabs]]

### Multi-Identity Users

Link multiple identifiers (email, Slack ID, etc.) to a single user:

[[tabs]]

[[tab Python]]
```python
# Link email, Slack ID, and internal ID to same user
result = formation.associate_user_identifiers(
    identifiers=[
        "alice@email.com",
        {"identifier": "U12345ABC", "type": "slack"},
        ("user_123", "internal"),
    ],
    user_id=None,  # Create new user (or pass existing user ID)
)
print(f"User ID: {result['muxi_user_id']}")

# Now all identifiers resolve to the same user
formation.chat("Hello", user_id="alice@email.com")  # Same user
formation.chat("Hello", user_id="U12345ABC")        # Same user
formation.chat("Hello", user_id="user_123")         # Same user
```
[[/tab]]

[[tab TypeScript]]
```typescript
// Link identifiers to same user
const result = await formation.associateUserIdentifiers({
  identifiers: [
    "alice@email.com",
    { identifier: "U12345ABC", type: "slack" },
  ],
  userId: null,  // Create new user
});
console.log(`User ID: ${result.muxiUserId}`);
```
[[/tab]]

[[tab Go]]
```go
result, _ := client.AssociateUserIdentifiers(ctx, &muxi.AssociateRequest{
    Identifiers: []interface{}{
        "alice@email.com",
        muxi.TypedIdentifier{Identifier: "U12345ABC", Type: "slack"},
    },
    UserID: nil,
})
fmt.Printf("User ID: %s\n", result.MuxiUserID)
```
[[/tab]]

[[/tabs]]

**Why?** Users interact via multiple channels (Slack, email, web). Multi-identity ensures they get the same memory and context everywhere.

[Learn more: Multi-Identity Users →](../concepts/multi-identity.md)

### Session Restore

Restore conversation history from your external storage:

[[tabs]]

[[tab Python]]
```python
# Restore session from your database
messages = your_db.get_messages(session_id="sess_abc123")

formation.restore_session(
    session_id="sess_abc123",
    messages=[
        {"role": "user", "content": "Hello", "timestamp": "..."},
        {"role": "assistant", "content": "Hi!", "timestamp": "..."},
    ],
    user_id="alice@email.com"
)

# User continues with full context
```
[[/tab]]

[[tab TypeScript]]
```typescript
await formation.restoreSession({
  sessionId: "sess_abc123",
  messages: [
    { role: "user", content: "Hello", timestamp: "..." },
    { role: "assistant", content: "Hi!", timestamp: "..." },
  ],
  userId: "alice@email.com",
});
```
[[/tab]]

[[tab Go]]
```go
client.RestoreSession(ctx, &muxi.RestoreRequest{
    SessionID: "sess_abc123",
    Messages: []muxi.Message{
        {Role: "user", Content: "Hello", Timestamp: "..."},
        {Role: "assistant", Content: "Hi!", Timestamp: "..."},
    },
    UserID: "alice@email.com",
})
```
[[/tab]]

[[/tabs]]

**Why?** MUXI's buffer is ephemeral. For persistent chat history (like ChatGPT's sidebar), persist messages yourself and restore when users return.

[Learn more: Sessions →](../concepts/sessions.md)


## Error Handling

All SDKs provide typed errors:

| Error | Meaning |
|-------|---------|
| `AuthenticationError` | Invalid API key |
| `AuthorizationError` | Insufficient permissions |
| `NotFoundError` | Resource doesn't exist |
| `ValidationError` | Invalid request data |
| `RateLimitError` | Too many requests |
| `ServerError` | Server-side error |
| `ConnectionError` | Network issue |

[[tabs]]

[[tab Python]]
```python
from muxi import MuxiError, AuthenticationError, RateLimitError

try:
    response = formation.chat_stream({"message": "Hello!"}, user_id="user_123")
except AuthenticationError:
    print("Invalid API key")
except RateLimitError as e:
    print(f"Rate limited, retry after {e.retry_after}s")
except MuxiError as e:
    print(f"Error: {e.code} - {e.message}")
```
[[/tab]]

[[tab TypeScript]]
```typescript
import { MuxiError, AuthenticationError, RateLimitError } from "@muxi-ai/muxi-typescript";

try {
  await formation.chatStream({ message: "Hello!" }, "user_123");
} catch (err) {
  if (err instanceof AuthenticationError) {
    console.log("Invalid API key");
  } else if (err instanceof RateLimitError) {
    console.log(`Rate limited, retry after ${err.retryAfter}s`);
  } else if (err instanceof MuxiError) {
    console.log(`Error: ${err.code} - ${err.message}`);
  }
}
```
[[/tab]]

[[tab Go]]
```go
resp, err := client.Chat(ctx, &muxi.ChatRequest{Message: "Hello!", UserID: "u1"})
if err != nil {
    var authErr *muxi.AuthenticationError
    var rateLimit *muxi.RateLimitError

    switch {
    case errors.As(err, &authErr):
        log.Println("Invalid API key")
    case errors.As(err, &rateLimit):
        log.Printf("Rate limited, retry after %d seconds", rateLimit.RetryAfter)
    default:
        log.Fatal(err)
    }
}
```
[[/tab]]

[[/tabs]]


## Configuration

| Setting | Python | TypeScript | Go | Default |
|---------|--------|------------|-----|---------|
| Timeout | `timeout=30` | `timeout: 30000` | 30s built-in | 30s |
| Retries | `max_retries=3` | `maxRetries: 3` | 3 built-in | 3 |
| Debug | `debug=True` | `debug: true` | - | Off |


## Learn More

- [Python SDK →](python-sdk.md)
- [TypeScript SDK →](typescript-sdk.md)
- [Go SDK →](go-sdk.md)
- [Ruby SDK →](ruby-sdk.md)
- [PHP SDK →](php-sdk.md)
- [C# SDK →](csharp-sdk.md)
- [Java SDK →](java-sdk.md)
- [Kotlin SDK →](kotlin-sdk.md)
- [Swift SDK →](swift-sdk.md)
- [Dart SDK →](dart-sdk.md)
- [Rust SDK →](rust-sdk.md)
- [C++ SDK →](cpp-sdk.md)

**Reference:**
- [API Reference →](../reference/api-reference.md)
