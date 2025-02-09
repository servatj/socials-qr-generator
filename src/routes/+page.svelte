<script lang="ts">
	let inputValue = '';
	let selectedNetwork: 'whatsapp' | 'instagram' | 'linkedin' | 'twitter' = 'whatsapp';

	function generateSocialQR(value: string, network: string): string {
		// Remove any non-alphanumeric characters except @ for handles
		const cleanValue = network === 'whatsapp' 
			? value.replace(/\D/g, '')
			: value.replace(/[^\w@]/g, '');
		
		// Create the social media URL based on the network
		const socialUrl = {
			whatsapp: `https://wa.me/${cleanValue}`,
			instagram: `https://instagram.com/${cleanValue}`,
			linkedin: `https://linkedin.com/in/${cleanValue}`,
			twitter: `https://twitter.com/${cleanValue}`
		}[network];
		
		// Create QR code URL using a free QR code API
		return `https://api.qrserver.com/v1/create-qr-code/?size=200x200&data=${encodeURIComponent(socialUrl)}`;
	}

	$: qrCodeUrl = inputValue ? generateSocialQR(inputValue, selectedNetwork) : '';
	
	$: placeholder = {
		whatsapp: 'Enter your phone number (e.g., 1234567890)',
		instagram: 'Enter your Instagram handle (e.g., username)',
		linkedin: 'Enter your LinkedIn username',
		twitter: 'Enter your X/Twitter handle (without @)'
	}[selectedNetwork];
</script>

<div class="container h-full mx-auto flex justify-center items-center">
	<div class="space-y-10 text-center flex flex-col items-center">
		<h2 class="h2">Select platform and enter your ID</h2>
		
		<!-- Social Network Selection Grid -->
		<div class="grid grid-cols-2 gap-4 w-full max-w-3xl">
			<button
				class="btn variant-filled h-24 {selectedNetwork === 'whatsapp' ? 'variant-filled-primary' : 'variant-filled-surface'}"
				on:click={() => selectedNetwork = 'whatsapp'}
			>
				WhatsApp
			</button>
			<button
				class="btn variant-filled h-24 {selectedNetwork === 'instagram' ? 'variant-filled-primary' : 'variant-filled-surface'}"
				on:click={() => selectedNetwork = 'instagram'}
			>
				Instagram
			</button>
			<button
				class="btn variant-filled h-24 {selectedNetwork === 'linkedin' ? 'variant-filled-primary' : 'variant-filled-surface'}"
				on:click={() => selectedNetwork = 'linkedin'}
			>
				LinkedIn
			</button>
			<button
				class="btn variant-filled h-24 {selectedNetwork === 'twitter' ? 'variant-filled-primary' : 'variant-filled-surface'}"
				on:click={() => selectedNetwork = 'twitter'}
			>
				X (Twitter)
			</button>
		</div>

		<!-- Input Field -->
		<div class="card p-4 variant-soft-primary border border-primary-500 w-full max-w-3xl">
			<div class="text-token">
				<input
					type="text"
					class="input w-full"
					{placeholder}
					bind:value={inputValue}
				/>
			</div>
		</div>
		
		<!-- QR Code Display -->
		{#if qrCodeUrl}
			<div class="card p-4 variant-soft-secondary">
				<img 
					src={qrCodeUrl} 
					alt="Social QR Code"
					class="mx-auto"
					width="200"
					height="200"
				/>
				<p class="mt-2 text-sm">Scan to open {selectedNetwork} profile</p>
			</div>
		{/if}
	</div>
</div>

<style lang="postcss">
	figure {
		@apply flex relative flex-col;
	}
	figure svg,
	.img-bg {
		@apply w-64 h-64 md:w-80 md:h-80;
	}
	.img-bg {
		@apply absolute z-[-1] rounded-full blur-[50px] transition-all;
		animation: pulse 5s cubic-bezier(0, 0, 0, 0.5) infinite,
			glow 5s linear infinite;
	}
	@keyframes glow {
		0% {
			@apply bg-primary-400/50;
		}
		33% {
			@apply bg-secondary-400/50;
		}
		66% {
			@apply bg-tertiary-400/50;
		}
		100% {
			@apply bg-primary-400/50;
		}
	}
	@keyframes pulse {
		50% {
			transform: scale(1.5);
		}
	}
</style>
