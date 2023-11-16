<script lang="ts">
	import { Circle, File, Laptop, Moon, Sun } from "radix-icons-svelte";
	import * as Command from "@/registry/new-york/ui/command";
	import { Button } from "@/registry/new-york/ui/button";
	import { onMount } from "svelte";
	import { cn } from "@/utils";
	import { docsConfig } from "@/config/docs";
	import { goto } from "$app/navigation";
	import { resetMode, setMode } from "mode-watcher";

	let open = false;

	onMount(() => {
		function handleKeydown(e: KeyboardEvent) {
			if (e.key === "k" && (e.metaKey || e.ctrlKey)) {
				e.preventDefault();
				open = true;
			}
		}
		document.addEventListener("keydown", handleKeydown);

		return () => {
			document.removeEventListener("keydown", handleKeydown);
		};
	});

	function runCommand(cmd: () => void) {
		open = false;
		cmd();
	}

	const mainNav = docsConfig.mainNav.filter((item) => !item.external);
	const sidebarNav = docsConfig.sidebarNav;
</script>

<Button
	variant="outline"
	class={cn(
		"relative w-full justify-start text-sm text-muted-foreground sm:pr-12 md:w-40 lg:w-64"
	)}
	on:click={() => (open = true)}
	{...$$restProps}
>
	<span class="hidden lg:inline-flex"> Search documentation... </span>
	<span class="inline-flex lg:hidden">Search...</span>
	<kbd
		class="pointer-events-none absolute right-1.5 top-1.5 hidden h-5 select-none items-center gap-1 rounded border bg-muted px-1.5 font-mono text-[10px] font-medium opacity-100 sm:flex"
	>
		<span class="text-xs">⌘</span>K
	</kbd>
</Button>
<Command.Dialog bind:open>
	<Command.Input placeholder="Type a command or search" />
	<Command.List>
		<Command.Empty>No results found.</Command.Empty>
		<Command.Group heading="Links">
			{#each mainNav as navItem}
				<Command.Item
					value={navItem.title}
					onSelect={() =>
						runCommand(() => {
							navItem.href && goto(navItem.href);
						})}
				>
					<File class="mr-2 h-4 w-4" />
					{navItem.title}
				</Command.Item>
			{/each}
		</Command.Group>
		{#each sidebarNav as group}
			<Command.Group heading={group.title}>
				{#each group.items as navItem}
					<Command.Item
						value={navItem.title}
						onSelect={() =>
							runCommand(() => {
								navItem.href && goto(navItem.href);
							})}
					>
						<div
							class="mr-2 flex h-4 w-4 items-center justify-center"
						>
							<Circle class="h-3 w-3" />
						</div>
						{navItem.title}
					</Command.Item>
				{/each}
			</Command.Group>
		{/each}
		<Command.Separator />
		<Command.Group heading="Theme">
			<Command.Item
				value="light"
				onSelect={() => runCommand(() => setMode("light"))}
			>
				<Sun class="mr-2 h-4 w-4" />
				Light
			</Command.Item>
			<Command.Item
				value="dark"
				onSelect={() => runCommand(() => setMode("dark"))}
			>
				<Moon class="mr-2 h-4 w-4" />
				Dark
			</Command.Item>
			<Command.Item
				value="system"
				onSelect={() => runCommand(() => resetMode())}
			>
				<Laptop class="mr-2 h-4 w-4" />
				System
			</Command.Item>
		</Command.Group>
	</Command.List>
</Command.Dialog>