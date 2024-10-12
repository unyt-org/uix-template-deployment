# UIX Template: Deployment

Deploying [UIX](https://github.com/unyt-org/uix) apps is an essential part of taking your project into production, and while the [unyt.app](https://unyt.app) infrastructure is a great solution, there are several ways to host your own UIX projects outside of it.

**Note**:
Unlike some frameworks, UIX doesn’t involve a traditional "build" process. UIX projects are designed to run directly using the `uix` command, which makes deployment straightforward. Instead of compiling your project, you’ll be able to directly run it on any machine that has UIX installed.

You can upload your UIX project to your machine using tools like `scp`, serve UIX at a specific port using the `uix`-CLI and configure your backbone infrastructure (such as `Nginx` reverse proxy) to forward incoming HTTP requests to the selected port. *Voilà - your app is live!*

If you don't like to setup all the infrastructure on your own, we recommend deploying apps using the [UIX Docker Host](https://github.com/unyt-org/docker-host) inside containers for better portability and management.

## Deploying UIX with the Docker Host
> [!NOTE]
> Docker Hosts are only support on Linux systems.
> If you experience some issues with your Linux distribution please let us know.

Follow these steps to launch your UIX app in minutey. For more information check out the [Docker Host repository](https://github.com/unyt-org/docker-host/tree/v2).

1. Install Docker on your server:
	
	Follow the [Docker installation guide](https://docs.docker.com/get-started/get-docker/) for your server's operating system.
2. Install the Docker Host Endpoint on your server:

	```bash
	curl -s https://raw.githubusercontent.com/unyt-org/docker-host/main/setup.sh | bash -s @+YOUR_DOCKER_HOST
	```
	Make sure to pass a unique [endpoint id](https://docs.unyt.org/manual/datex/endpoints) to the install script. The setup script will create a Docker Host instance by installing [Deno](https://github.com/denoland/deno) and creating a persistent service inside of `etc/systemd/system`.

	You can check if the installation was successfull using the following command: 
	```bash
	systemctl status unyt_docker_host_HOST_ENDPOINT.service
	```
3. Configure your UIX app to use the Docker Host:

	After setting up the Docker Host on your server, add the host endpoint as location into your UIX apps `backend/.dx` configuration file.

	```datex title="backend/.dx"
	use stage from #public.uix;

	endpoint: stage {
		prod: @+your_app
	},

	location: stage {
		prod: @+YOUR_DOCKER_HOST
	},

	domain: stage {
		prod: 'YOUR-DOMAIN.com'
	}
	```
4. Deploy the UIX app into production on your local machine:

	With the Docker host and `.dx` configured, deploy your app from your local machine by running:
	```bash
	uix --stage prod
	```

	This command will deploy your UIX app to your custom Docker host, making it live on your custom domain.
> [!WARNING]
> If the Docker Host you plan to deploy to has a access token configured, you need to pass this access token to UIX to make sure your app can authenticate.<br/>
> You can set the access token as `HOST_TOKEN` environment variable on your local UIX projects console.
> ```bash
> export HOST_TOKEN=YOUR_TOKEN
> ```

---

<sub>&copy; unyt 2024 • [unyt.org](https://unyt.org)</sub>
