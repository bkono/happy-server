# Happy Server

Minimal backend for open-source end-to-end encrypted Claude Code clients.

## What is Happy?

Happy Server is the synchronization backbone for secure Claude Code clients. It enables multiple devices to share encrypted conversations while maintaining complete privacy - the server never sees your messages, only encrypted blobs it cannot read.

## Features

- 🔐 **Zero Knowledge** - The server stores encrypted data but has no ability to decrypt it
- 🎯 **Minimal Surface** - Only essential features for secure sync, nothing more  
- 🕵️ **Privacy First** - No analytics, no tracking, no data mining
- 📖 **Open Source** - Transparent implementation you can audit and self-host
- 🔑 **Cryptographic Auth** - No passwords stored, only public key signatures
- ⚡ **Real-time Sync** - WebSocket-based synchronization across all your devices
- 📱 **Multi-device** - Seamless session management across phones, tablets, and computers
- 🔔 **Push Notifications** - Notify when Claude Code finishes tasks or needs permissions (encrypted, we can't see the content)
- 🌐 **Distributed Ready** - Built to scale horizontally when needed

## How It Works

Your Claude Code clients generate encryption keys locally and use Happy Server as a secure relay. Messages are end-to-end encrypted before leaving your device. The server's job is simple: store encrypted blobs and sync them between your devices in real-time.

## Hosting

**You don't need to self-host!** Our free cloud Happy Server at `happy-api.slopus.com` is just as secure as running your own. Since all data is end-to-end encrypted before it reaches our servers, we literally cannot read your messages even if we wanted to. The encryption happens on your device, and only you have the keys.

That said, Happy Server is open source and self-hostable if you prefer running your own infrastructure. The security model is identical whether you use our servers or your own.

## Running It

Happy Server requires **Postgres**, **Redis**, and an **S3-compatible** object store (tested with MinIO). The server will fail to boot unless these are reachable and the S3 bucket exists.

### Required environment variables

- `DATABASE_URL` (Postgres connection string)
- `REDIS_URL` (Redis connection string)
- `HANDY_MASTER_SECRET` (used to derive auth/encryption keys)
- `S3_HOST`, `S3_PORT`, `S3_USE_SSL` (S3 endpoint settings)
- `S3_REGION` (required for AWS if your bucket is not in `us-east-1`)
- `S3_ACCESS_KEY`, `S3_SECRET_KEY` (S3 credentials)
- `S3_BUCKET` (bucket must already exist)
- `S3_PUBLIC_URL` (base URL returned to clients, e.g. `http://localhost:9000/happy`)

Optional (feature-gated):
- GitHub connect/webhooks: `GITHUB_APP_ID`, `GITHUB_PRIVATE_KEY`, `GITHUB_CLIENT_ID`, `GITHUB_CLIENT_SECRET`, `GITHUB_REDIRECT_URI` (or `GITHUB_REDIRECT_URL`), `GITHUB_WEBHOOK_SECRET`
- Voice: `ELEVENLABS_API_KEY`
- Metrics: `METRICS_ENABLED` (defaults to enabled), `METRICS_PORT` (defaults to `9090`)

### Local development (host process + Docker deps)

1. Start dependencies:
   - `yarn db`
   - `yarn redis`
   - `yarn s3`
   - `yarn s3:init`
2. Configure env: copy/edit `.env.dev` (it includes working local defaults).
3. Apply DB migrations: `yarn migrate` (or `yarn prisma migrate deploy`).
4. Start the server: `yarn dev` (or `yarn start`).

### Running the server in Docker

Build and run the server container, pointing it at your Postgres/Redis/MinIO.

- If your dependencies are running on your host machine, use `host.docker.internal` on macOS/Windows for `DATABASE_URL`, `REDIS_URL`, and `S3_HOST`.
- If your dependencies are in the same Docker network, use the dependency container/service names.

Note: the image runs `prisma migrate deploy` on startup. If you prefer managing migrations separately, override the container command and run migrations yourself.

## License

MIT - Use it, modify it, deploy it anywhere.
